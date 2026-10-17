# Finding 01 — Two unlock techniques, and why conflating them wastes weeks

**Excerpt from the technical pack.** Demonstrates method and depth. Patch data, marker
sets and profile tables are withheld at this tier — the amount withheld is stated at the
end so you can judge what you are missing.

---

## 1. Start with something you can check

In the vendor's bootloader the unlock path is gated by a marker string set. One of those
strings contains a **typo**:

```
frp paritition does not exist
        ^^
```

That is `paritition`, misspelled. It is a diagnostic message in the bootloader, it ships
in the shipped firmware, and nothing else in the world produces that byte sequence.

**Check it yourself.** Take any firmware dump for the relevant device family and search
for the ASCII string `frp paritition`. If it is not there, this research is wrong and you
have spent an hour learning that cheaply.

That string is the anchor for the whole technique-A identification process: it is
present in exactly the profiles that use the flag-based lock path, and absent in those
that do not. Whoever produced the original tool found it by reading strings — the
difficulty is not the discovery, it is knowing what to do with it.

---

## 2. The architectural insight

Most writing on this subject treats MediaTek's test mode (META) as an exploit. It is not.
It is a **factory interface**, shipped deliberately, and its position in the boot order is
what makes it useful.

```
BROM  (in silicon, immutable)
 └── preloader           ← reads a boot-mode value
      ├── META / ATE     ← the test runtime, for calibration and provisioning
      └── LK             ← the bootloader. THE LOCK LIVES HERE.
           └── kernel
                └── Android
```

META and LK are **siblings**. META runs *instead of* the boot path, not beneath it.

Consequences, in order of how much they are worth:

1. When META is running, **LK has never executed**, so `get_lock_state()` has never been
   called and the lock decision has never been made.
2. Android has never executed, so there is no SELinux policy, no `ro.secure`, no ADB
   authorisation to satisfy.
3. META has direct block-device access **because that is its documented purpose** — it
   must write IMEI and read RF calibration before any OS exists.

So partition-level read and write on a locked retail phone is a consequence of the boot
order, not a vulnerability. This reframes the entire acquisition problem: you are not
looking for a bug, you are learning to speak a supported protocol.

That reframing is the single most valuable conclusion in this body of work, because it
tells you where to spend effort. A researcher who thinks it is an exploit hunts for
memory corruption. A researcher who understands the boot order learns the handshake and
is finished in days.

---

## 3. The two techniques

Once you can read and write partitions, unlocking becomes a question of *which* partition
holds the decision — and that differs by vendor.

| | **A — lock-state constant patch** | **B — key substitution** |
|---|---|---|
| The gate | bootloader reads a lock **flag** | bootloader verifies a **signed token** |
| Found in | Transsion-family firmware | token-gated firmware |
| The change | one instruction constant | RSA modulus + certificate block |
| Bytes changed | **4** | **~256**, plus per-partition certificate rewrite |
| Signing needed | no | **yes**, offline, with your own key |
| Image size | unchanged | must remain **exactly** equal |

### Why they are not interchangeable

Applying A to a B device does nothing — the flag is read, ignored, and the token check
fails anyway. You get "unlock succeeded" from the tool, and a device that boots locked.
That failure looks like a bad patch and sends you debugging the wrong thing.

Applying B to an A device is worse: you rewrite a certificate block that nothing verifies,
and the image no longer matches its own hashes.

### The decision that comes first

```
Does the bootloader verify an RSA/ECDSA token?
├── YES → B. You will need: the vendor reference public key (it is the locator),
│          certificate-block parsing, and an offline signing step
└── NO, it reads a lock flag
    └── A. You will need: an instruction pattern or a string anchor, and a
              size-preserving patch
```

Determine this **before** collecting images. It changes what you must acquire — B needs
the reference public key, A does not.

---

## 4. Technique A in outline (data withheld)

The method, without the recipe:

**Locate.** Score candidate sub-images inside the bootloader container by name, size,
architecture tell-tales and marker-string presence. The winner is the bootloader proper.

**Find the store.** The lock state is written by a short instruction sequence: load a
constant, then store it. The constant encodes the state — and one particular value means
"locked". Finding it is a pattern scan, and the pattern is stable across firmware
revisions for a given architecture.

**Patch.** Replace the constant with the "unlocked" value. **Size-preserving.** The
partition is fixed-size and the image must not grow.

**Verify before writing.** Three gates: the site still contains the expected original
bytes, the patched image parses as the same container, and the patched length equals the
original. Any mismatch is a rejection, not a warning.

**Write both slots** if the device is A/B. Patching only the live slot is the single most
common cause of "it worked, then it came back".

### The failure mode worth publishing

There is a tempting shortcut: instead of changing the constant, neutralise the branch
that consumes it. **This is wrong, and it is a brick.**

The branch result lands in a register. Neutralising the branch leaves that register
holding whatever was there before — on the observed builds, a stack pointer. The branch
is then taken anyway, and the boot path aborts.

The correct fix is to **set the value**, not skip the check. This is documented in the
pack with the specific instance annotated, and it is the kind of error that costs a
device per attempt to discover.

---

## 5. Technique B in outline (data withheld)

Different problem, different shape.

**Locate.** Not by pattern — by exact byte search for the **vendor's reference public
key modulus**. This is the elegant part: you do not need to find the verification logic
at all. Ship the vendor's public key, search for its bytes, and you have found the
verification site. No instruction scanning required.

**Replace.** Splice your own modulus in at that offset. This is why size must be exactly
equal: a larger key shifts every downstream offset and invalidates the container header,
the partition table and every hash.

**Bypass the certificate block.** The bootloader also checks a per-partition certificate
covering the image hashes. Two strategies exist — one wraps the original bytes so a parse
still succeeds and appends a corrected copy; the other prepends a hash-override block.
Both are documented with their trade-offs.

**Sign.** Offline. The unlock token comes from the device; you sign it with the private
key matching the modulus you spliced in, using raw PKCS#1 v1.5. The critical discipline:
**verify your own signature before flashing it.** A wrong signature byte produces an
unlock failure indistinguishable from a wrong patch.

### The traps

Three defects found in a public implementation of this technique, all of which will bite
anyone reimplementing from scratch:

1. **Equal-length assumption.** The splice silently shifts offsets if the new key size
   differs from the reference key. The upstream README claims any key size works. It does
   not.
2. **Single-hit search.** The modulus may appear more than once — multi-slot images,
   mirrored certificates. Replacing only the first occurrence leaves a verification path
   intact.
3. **Shallow verification.** Checking "my modulus is present" and "the image parses" does
   not confirm the patched certificates are consistent with the patched content.

Each is specified with a fix in the pack.

---

## 6. What is withheld at this tier

Stated explicitly so you can size the gap:

| Item | Status here |
|---|---|
| Decision framework and method | ✅ shown in full |
| Architectural conclusion | ✅ shown in full |
| The checkable marker string | ✅ shown |
| Instruction pattern family | ✅ described, not given as bytes |
| **Exact patch byte sequences per architecture** | ✖ withheld |
| **Complete marker string sets per profile** | ✖ withheld |
| **The 5 device profile definitions** | ✖ withheld |
| **Scoring weights and thresholds** | ✖ withheld |
| **Certificate-block layouts** | ✖ described, not given |
| **Partition offset tables** | ✖ withheld |

You have enough here to understand the work, judge its quality, and decide whether it is
worth buying. You do not have enough to skip the work. That is the intended boundary.

---

## 7. What this demonstrates

Not knowledge — anyone can read a wiki. Three things:

1. **You can verify one claim cheaply.** The typo test costs you an hour and settles
   whether to keep reading.
2. **The conclusion is non-obvious and consequential.** The sibling-boot-order insight
   changes where effort goes. That is the difference between months and days.
3. **The failure modes are documented.** A real practitioner knows the wrong way. Listing
   three specific traps with fixes is not something that can be faked from public sources,
   because these are things you learn by breaking them.
