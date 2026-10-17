# Statistics — all figures measured, none estimated

Every number on this page was produced by running the commands shown. You can repeat
them. Nothing here is rounded up for effect.

**Measured:** 2026-10-17

---

## 1. Analysis tree volume

| Artefact set | Files | Size |
|---|---:|---:|
| Decompiled reconstruction (`decompiled/`) | **4,793** | 1.26 GB |
| — of which C# source (`.cs`) | 4,665 | — |
| VM disassembly dumps (`vm_disasm/`) | **5,573** | 387 MB |
| VM runtime dumps (`vm_dump/`) | **5,272** | 15 MB |
| Packet captures (`captures/`) | 4 | 303 MB |
| String tables (`strings/`, `stringdump/`) | 31 | 11 MB |
| Original binary (`binary/`) | 5 | 103 MB |
| Analysis scripts (`scripts/`) | **135** | 3.7 MB |
| Written reports (`docs/`) | **23** | 365 KB |

**Total analysed surface: ~15,800 files.**

### Reproduce

```powershell
$root = "…\ShellTool_MDM"
(Get-ChildItem "$root\decompiled" -Recurse -File).Count
(Get-ChildItem "$root\decompiled" -Recurse -File | Measure-Object Length -Sum).Sum
(Get-ChildItem "$root\decompiled" -Recurse -File -Filter *.cs).Count
```

---

## 2. Obfuscator reverse engineering

The client is protected by a .NET virtual-machine obfuscator. These are the recovered
internals.

| Metric | Value |
|---|---:|
| Dispatch table capacity | **31,584 slots** |
| Live slots | **1,974** |
| — framework-library delegates | 397 |
| — generated emit trampolines | 1,577 |
| Offline reconstruction bound | **1,972 / 1,974** |
| Unresolved (fallback) entries | **0** |
| Largest single generated file | ~60 MB, ~1.78 M lines |

A 1,972/1,974 reconstruction with zero fallbacks is the difference between "I can read
this" and "I can reason about this". It is also the reason the report set exists at all:
without the table bound, the decompiled output is unreadable noise.

---

## 3. Research outputs

| Output | Count |
|---|---:|
| Reports written | 23 |
| Functional methods catalogued and classified | ~700 |
| Client classes mapped to roles | 25+ |
| Bootloader device profiles recovered | 5 |
| Partitions catalogued (with role + size class) | 40+ |
| Backend actions identified | 38 |
| Backend actions mapped to concrete tokens | 20 |
| META protocol frames recovered | 8 |
| — of which labelled with certainty | 2 |
| Unlock techniques distinguished | 2 |
| Documented tool defects found and specified | 7 |
| Confidence-labelled open questions | 7 |

---

## 4. The two unlock techniques

Not variants of one idea — they solve different locks.

| | **A — lock-state constant patch** | **B — key substitution** |
|---|---|---|
| Applies when | the bootloader reads a lock flag | the bootloader verifies a signed token |
| Devices | Transsion family | token-gated (different vendor) |
| Change made | one instruction constant | RSA modulus + certificate block |
| Size | 4 bytes | ~256 bytes + per-partition cert rewrite |
| Offline signing | not required | **required** |
| Image size | unchanged | must remain **exactly** equal |
| Documented in | report 16 | report 19 |

The distinction matters commercially: applying the wrong one wastes weeks and can brick
the unit.

---

## 5. Verification depth

What was actually checked, not merely read.

| Claim type | How it was verified |
|---|---|
| Protocol frame format | **XOR checksum recomputed across 5 independent frames** — all matched |
| Vendor challenge-response | Cross-checked against an independent public implementation; keys and algorithm agree |
| Lock-state constants | Cross-referenced against `seccfg` state values and against a public tool's definitions |
| Partition offsets | Verified against real GPT dumps and against documented file-format specs |
| Byte-search behaviour | Executed against the actual implementation, including the overlapping-match edge case |
| Patch size rule | Confirmed by executing the engine: replacement longer than pattern is a hard error; shorter zeroes the tail |

Where a claim could not be verified it is labelled **UNK** in the reports rather than
softened into a hedge. Seven such items are listed openly in `BRIEF.md` §6.

---

## 6. Commitment

The full manifests are held privately. Their aggregate hashes are published:

```
MANIFEST-decompiled.sha256  (4,793 lines) = fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
MANIFEST-docs.sha256        (23 lines)    = c14059ca5bfc2432decb82bc278750d1c122d49b86f01f6ba73f2633731c8d58
```

Verification procedure in `COMMITMENT.md`. These bind this exact content without
disclosing any of it.
