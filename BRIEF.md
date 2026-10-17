# MediaTek LK Unlock Research — Capability Brief

**What this is:** an independent reverse-engineering study of MediaTek bootloader
unlocking, and a working technical specification for reimplementing it.

**What it is not:** vendor source code, or a redistributable firmware tool.

**Prepared:** 2026-10-17 · **Status:** available for licence · **Contact:** TFast Digital

---

## 1. The finding, in one paragraph

MediaTek devices expose a factory test runtime called **META**. The bootloader lock is
enforced by **LK**, and LK and META are **siblings** — both are loaded by the
preloader, and META runs *instead of* the normal boot path. That single architectural
fact explains why partition-level access works on a locked, production-build phone: by
the time META is running, LK has never executed, so the lock decision has never been
made. Everything else in this body of work follows from it.

That is the kind of conclusion you can only reach by doing the work. It is also why most
public writing on this subject is wrong: it treats META as an exploit rather than as a
supported manufacturing interface.

---

## 2. What was actually done

A commercial phone-service client was analysed end to end — statically, and against a
complete decompiled reconstruction of its obfuscated codebase.

| Workstream | Output |
|---|---|
| Obfuscator analysis | Recovered the virtual-machine dispatch table: **31,584 slots, 1,974 live** (397 framework + 1,577 generated trampolines), reconstructed offline to 1,972/1,974 bound with zero fallbacks |
| Bootloader research | Two distinct unlock techniques identified and specified, with **5 device profiles**, marker-scoring algorithm, and exact patch semantics |
| Protocol reversing | Raw binary command frame format recovered (start byte, length, constant, XOR checksum) — verified against 5 independent frames |
| Partition semantics | 40+ partitions catalogued with roles, sizes, and which are safe to touch |
| Patch engine | Data model, parser rules, and search/apply semantics of the image-patching subsystem recovered |
| Backend contract | 38 server actions, 20 mapped to concrete tokens, envelope and payload shapes |
| Authentication | The vendor challenge-response recovered and cross-verified against independent public implementation |

**Volume:** 4,793 decompiled files (1.26 GB), 5,573 disassembly dumps, 135 analysis
scripts, 23 written reports totalling 365 KB. Full figures in `STATISTICS.md`.

---

## 3. Why this is hard to replicate

Not because the information is secret — most of it is discoverable. Because it costs
**months of specialised work** to get right, and the failure mode is a bricked device.

| Barrier | Reality |
|---|---|
| Obfuscation | The client runs a custom VM. Decompiled output is a *specification*, not a codebase — you cannot compile it. Reaching usable conclusions requires reading ~1.8M lines of generated state-machine code |
| Vocabulary | Recovering intent requires knowing MTK boot flow, AVB, EROFS/super partitions, ARM64 encoding, and RPMB — simultaneously |
| Correctness | A wrong patch byte bricks the unit. The published research includes a documented case where the *obvious* fix (NOP a branch) is wrong, because the branch result is consumed |
| Verification cost | Every conclusion needs a device to check against. That is capital and time, not reading |

---

## 4. What you get (licence dependent)

| Tier | Deliverable |
|---|---|
| **A — Brief** | This document |
| **B — Technical pack** | All 23 reports: architecture, protocols, partition maps, the two unlock techniques, the acquisition manual, the build playbook |
| **C — Implementation licence** | The above **plus** the extracted data: device profiles, patch site definitions, marker sets, partition tables |
| **D — Full assignment** | C plus the complete analysis tree (decompiled reconstruction, disassembly, scripts) and the right to extend it commercially |

Tier C is the one most buyers want: it is the difference between *knowing how* and
*having the data*. See `SCOPE.md`.

---

## 5. What is deliberately withheld at each tier

Stated plainly, because a buyer should know exactly what they are and are not getting.

| Item | Tier B | Tier C | Tier D |
|---|---|---|---|
| Technical reports | ✅ all 23 | ✅ | ✅ |
| Architecture and decision frameworks | ✅ | ✅ | ✅ |
| Device-profile patch data | ✖ summary only | ✅ | ✅ |
| Full marker sets and scoring tables | ✖ | ✅ | ✅ |
| Backend endpoint details | ✖ redacted | ✅ | ✅ |
| Decompiled reconstruction | ✖ | ✖ | ✅ |
| Vendor signing material | ✖ **not held** | ✖ **not held** | ✖ **not held** |

> **Important:** the vendor's unlock-token signing key is **not** part of this work and
> cannot be supplied by anyone. Any claim otherwise is false. What is supplied is
> knowledge of *which devices gate on a signature* and which do not — which determines
> whether you need one at all.

---

## 6. Honest limits

A brief that claims no gaps is not credible. These are the known open questions:

| Gap | Consequence |
|---|---|
| META command opcode table partially mapped (2 of 8 frames labelled) | Some META operations must be rediscovered per device |
| Whether the newest SoC generations gate META more strongly | **Untested.** Do not assume parity with mid-range parts |
| Vendor unlock signature | Not obtainable — by design |
| RPMB contents | Not readable without the RPMB key |
| One vendor's dex-neutralisation transform | Mechanism known, exact transform not recovered |

None of these block Tiers B or C for the device families documented. All are stated in
the reports themselves, with confidence labels, so you can audit rather than trust.

---

## 7. Verify before you buy

You should not take any of this on faith. Two things you can check immediately:

**1. The commitment hash.** The full analysis tree is committed by hash:

```
MANIFEST-decompiled.sha256 = fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
MANIFEST-docs.sha256       = c14059ca5bfc2432decb82bc278750d1c122d49b86f01f6ba73f2633731c8d58
```

That binds me to exactly this content. It reveals nothing. When I deliver under NDA, you
recompute the manifests and they must match — proving nothing was altered in between, and
that no substitution occurred. Procedure in `COMMITMENT.md`.

**2. A specific, checkable detail.** §1 of `FINDINGS/01-unlock-techniques.md` describes a
non-obvious property of the vendor marker set. Check it against any firmware dump you
already hold. If it is not there, I am wrong and you have lost nothing but an hour.

---

## 8. Terms in short

- NDA before Tier B. Mutual, standard.
- Tier C and D priced per scope, milestone-based.
- Source escrow available so you are never stranded without access.
- Per-recipient watermarking in every delivered copy.
- **No** redistribution rights below Tier D.
- Independent-reconstruction provenance documented — see `SCOPE.md` §4.

Detailed in `TERMS.md`.

---

## 9. What this is not

To be unambiguous:

- It is **not** the vendor's source code. It is an independent reconstruction and
  behavioural specification produced by analysis.
- It is **not** a finished product. It is a specification and dataset that removes
  months of research from your build.
- It does **not** include any vendor signing capability.
- It is **not** for resale to end users. Tier D is required for commercial distribution.

If any of those are what you need, say so now and we will not waste each other's time.
