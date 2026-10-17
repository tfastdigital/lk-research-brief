# Finding 02 — Reversing an undocumented binary protocol from a VM-obfuscated client

**Excerpt from the technical pack.** Shows the protocol-recovery method. Frame constants
and opcode semantics beyond two labelled frames are withheld.

---

## 1. The problem

The client talks to the device's test runtime over a binary protocol with no
documentation, no public spec, and no visible encoders — because the encoders were inside
a virtual machine that obfuscated every call site.

A naive approach fails immediately. You see:

```csharp
((F8AFAF8E)M0B9011C.L68FD08C.B437EC30[1570])(ref stateMachine.CA237D84);
```

That is not code you can read. Every method call, every string operation, every loop
condition is a delegate dispatch through an index into a 31,584-slot table. There are no
locals you recognise, no names, no control flow you can follow.

So how do you recover a protocol from this?

---

## 2. The method: find the constant, then work outward

You do not reverse the protocol. You reverse **one frame**, then generalise by pattern.

### Step 1 — locate literal byte arrays

Obfuscators hide *calls* well. They hide *data* badly. A protocol constant is a literal
that has to exist somewhere, and it survives as a hex string or a byte array.

Search the decompiled tree for the shape of the thing you are looking for — strings of hex
with a shared prefix. In this case, the repeated leading byte was enough:

```
55 00 10 66 0c 00 00 00 15 00 8f 00 00 00 00 00 30 00 00 00 85   (shown in full)
55 00 08 66 04 00 00 00 01 00 ?? ...                             bb
55 00 0c 66 08 00 00 00 01 00 08 00 ?? ...                       3e
55 00 18 66 0c 00 08 00 42 00 ?? ...                             15
```

Four constants, all starting `55`, all with `66` early. That is a header. (One frame is
shown completely because the checksum proof below needs it; the operand bytes of the
other three are withheld at this tier. Verification was performed on five real frames.)

### Step 2 — line them up

This is the whole technique. Put them in a table, one byte per column:

```
55  00  0c  66  08 00 00 00  01 00 08 00 ?? ...                    3e
55  00  08  66  04 00 00 00  01 00 ?? ...                          bb
55  00  18  66  0c 00 08 00  42 00 ?? ...                          15
55  00  10  66  0c 00 00 00  15 00 8f 00 00 00 00 00 30 00 00 00  85
```

Now the structure reads itself:

| Offset | Bytes | Behaviour across samples | Conclusion |
|---|---|---|---|
| 0 | `55` | constant | **start byte** |
| 1 | `00` | constant here | flags / variant |
| 2 | `0c`,`08`,`18`,`10` | **tracks total length** | **length field** |
| 3 | `66` | constant | **type marker** |
| 4+ | varies | opcode + operands | **payload** |
| last | varies | — | **candidate checksum** |

### Step 3 — test the checksum hypothesis

The last hypothesis is the one that either validates the whole reconstruction or kills it.
If byte 0 is a start marker and byte 2 is a length, the trailing byte is very likely a
checksum over the preceding bytes.

XOR is the first thing to try. Compute it:

```
Frame:  55 00 10 66 0c 00 00 00 15 00 8f 00 00 00 00 00 30 00 00 00 85

XOR of bytes[0 .. n-2]:
  55 ^ 00 = 55
  55 ^ 10 = 45
  45 ^ 66 = 23
  23 ^ 0c = 2f
  2f ^ 15 = 3a
  3a ^ 8f = b5
  b5 ^ 30 = 85

Trailing byte = 85.   Match.
```

Then **repeat on the other frames** — because one match is a coincidence:

```
Frame 2  (operands withheld)            XOR = bb   ✓ trailing bb
Frame 3  (operands withheld)            XOR = 3e   ✓ trailing 3e
Frame 4  (operands withheld)            XOR = 15   ✓ trailing 15
Frame 5  (operands withheld)            XOR = 22   ✓ trailing 22
```

Five independent frames, five matches. The checksum hypothesis is now **verified**, not
assumed.

That is the moment the protocol stops being opaque. You can now construct valid frames
for operations you have never seen, which is what "reversing a protocol" actually means.

### Step 4 — recover the length rule

Byte 2 vs total length:

| Total | Byte 2 | Difference |
|---:|---:|---:|
| 21 | `0x10` = 16 | 5 |
| 13 | `0x08` = 8 | 5 |
| 29 | `0x18` = 24 | 5 |

Consistent: **length = total − 5** when a checksum is present. (An earlier formulation
said `total − 4`; the samples show that variant belongs to a helper that appends the
checksum itself rather than including it in the count. Both appear in the code, which is
why the rule had to be pinned against real frames rather than inferred.)

### Step 5 — find the encoder, confirm the model

Now you can search *with knowledge*. The encoder was hidden in the VM, but its callers
are visible, and its call graph has a shape:

```
ShellCommand(cmd)
  └─> frame-send(cmd)
        └─> vm-encode(cmd)        ← hidden in the VM
              └─> transport-send(frame)
```

The existence and shape of that chain — recovered by following callers, not callees — is
what proves the shell text is *frame-encoded* rather than passed to a plain shell. That
distinction matters: it is why the same command set runs without an ADB authorisation.

---

## 3. Why this method generalises

The approach is not protocol-specific. It is:

1. **Find literals** — data survives obfuscation better than logic.
2. **Align by similarity** — table the candidates, read structure off the columns that
   do not vary in the same places.
3. **Hypothesise the invariant** — checksum, length, type.
4. **Verify on independent samples** — one match proves nothing; five matching frames
   prove the model.
5. **Find the encoder last** — once you know the format, searching for it is trivial.

Step 4 is where most analyses go wrong. Recovering a plausible format from one example
and stopping produces a model that works on the example and fails in the field.

---

## 4. Corroboration

The recovered format was checked against an independent public implementation of the same
device protocol. The mode-selection strings and the overall handshake shape agree, which
means the reconstruction is not an artefact of one particular build.

Where the public implementation and the client diverge — different vendor credentials,
additional states — the divergence is documented rather than smoothed over. Divergence is
*evidence*, not noise: it tells you which parts are protocol and which are vendor policy.

---

## 5. What is withheld at this tier

| Item | Status |
|---|---|
| The recovery method | ✅ shown in full |
| Header structure and field meanings | ✅ shown in full |
| Checksum algorithm with worked verification | ✅ shown in full |
| Length rule | ✅ shown |
| **The complete frame constant table** | ✖ withheld |
| **Opcode semantics beyond 2 labelled frames** | ✖ withheld |
| **Vendor authentication exchange internals** | ✖ withheld |
| **Which commands map to which operations** | ✖ withheld |

The method is the transferable asset, so you get it. The recovered table is the work
product, so you do not.

---

## 6. What this demonstrates

1. **A verified model, not a guess.** Five frames, five checksum matches, length rule
   extracted and reconciled against a real inconsistency. This is falsifiable work.
2. **The obfuscation was actually defeated.** You cannot get to step 5 without resolving
   the dispatch table. The method section is only writable by someone who did it.
3. **The distinction that matters commercially.** Understanding that the shell is
   frame-encapsulated — and therefore not subject to ADB authorisation — is the insight
   that explains why the whole approach works at all. It is also the sentence most likely
   to be copied, which is why the surrounding data stays withheld.
