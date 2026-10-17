# MediaTek LK Unlock Research — Buyer Package

This repository is a **capability brief**: a demonstration that a substantial body of
reverse-engineering work exists, presented so you can assess it without receiving it.

Nothing here is a deliverable. It is the evidence you need to decide whether to buy.

---

## Read in this order

| # | Document | Time | Purpose |
|---|---|---|---|
| 1 | **[`BRIEF.md`](BRIEF.md)** | 10 min | The finding, the volume, what is for sale, what is withheld, honest limits |
| 2 | **[`STATISTICS.md`](STATISTICS.md)** | 5 min | Every figure measured and reproducible — no estimates |
| 3 | **[`COMMITMENT.md`](COMMITMENT.md)** | 10 min | How the content is proven to exist without revealing it |
| 4 | **[`FINDINGS/01-unlock-techniques.md`](FINDINGS/01-unlock-techniques.md)** | 15 min | Worked example: two techniques, one checkable claim, documented failure modes |
| 5 | **[`FINDINGS/02-protocol-recovery.md`](FINDINGS/02-protocol-recovery.md)** | 15 min | Worked example: recovering a binary protocol from an obfuscated client |
| 6 | **[`SCOPE.md`](SCOPE.md)** | 10 min | Exactly what is in each tier, and the provenance statement |
| 7 | **[`TERMS.md`](TERMS.md)** | 10 min | Licence, confidentiality, escrow, limits |

---

## The claim in three lines

1. A MediaTek device's bootloader lock is enforced by LK; the factory test runtime **META**
   is loaded by the same preloader as a **sibling**, and runs *instead of* the boot path.
   So while META is running, LK has never executed and the lock decision has never been
   made. That is why partition-level access works on a locked retail phone — boot order,
   not a vulnerability.
2. Two **different** unlock techniques exist, for two different lock designs. Applying the
   wrong one wastes weeks and can brick the device. Both are specified.
3. The research covers **4,793 decompiled files**, 5,573 disassembly dumps, 135 analysis
   scripts and 23 written reports — with the obfuscator's dispatch table reconstructed to
   1,972 of 1,974 entries.

---

## Verify before you trust

**Do not take any of this on faith.** Two things you can check immediately, both cheap:

### A. The commitment

The full analysis tree is bound by hash:

```
MANIFEST-decompiled.sha256  (4,793 lines) = fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
MANIFEST-docs.sha256        (23 lines)    = c14059ca5bfc2432decb82bc278750d1c122d49b86f01f6ba73f2633731c8d58
```

At delivery you recompute the manifests and they must match exactly — proving nothing was
substituted in between. That commits me to the content while revealing none of it.
Procedure: [`COMMITMENT.md`](COMMITMENT.md).

### B. One specific, checkable detail

[`FINDINGS/01-unlock-techniques.md`](FINDINGS/01-unlock-techniques.md) §1 documents a
vendor bootloader marker string that contains a **typo**. Search any firmware dump you
already hold for it. If it is not there, this work is not what it claims and you have
spent an hour.

That test is deliberate. It costs you almost nothing and it either earns your attention
or ends the conversation.

---

## What is deliberately not here

Stated plainly, because a buyer deserves to know the boundary:

| Not in this package | Where |
|---|---|
| Patch byte sequences per architecture | Tier C |
| Complete marker string sets | Tier C |
| Device profile definitions | Tier C |
| Partition offset tables | Tier C |
| Scoring weights and thresholds | Tier C |
| Decompiled reconstruction | Tier D |
| Vendor signing key | **nowhere** — not held, not obtainable |

You get the method in full. You do not get the data. That boundary is the product.

---

## Honest limits

This research has genuine gaps, listed openly in [`BRIEF.md`](BRIEF.md) §6 — including
one that matters commercially: whether the newest SoC generations gate the test runtime
more strongly is **untested**, and should not be assumed. Every claim in the reports
carries a confidence label, and seven open questions are registered rather than hidden.

A brief that claims no gaps is not credible. Read the gaps; they are the reason to trust
the rest.

---

## Provenance

This is an **independent reconstruction and behavioural specification**, not vendor source
code. No vendor source was obtained. See [`SCOPE.md`](SCOPE.md) §4 before signing — it
determines what you can safely do with the material, and it is the one section a buyer
should not skip.

---
