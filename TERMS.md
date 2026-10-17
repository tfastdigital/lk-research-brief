# Terms

Commercial terms for licensing the MediaTek LK unlock research.

---

## 1. Licence tiers

| Tier | Deliverable | Price |
|---|---|---|
| **A** | Capability brief (this package) | free |
| **B** | 23 technical reports | **TBC** |
| **C** | Reports + extracted data + toolkit, one product line | **TBC** |
| **D** | Full assignment: everything + analysis tree + broad rights | **TBC** |

Payment: 50% on signing, 50% on delivery. Tier D may be split across three milestones.

---

## 2. What each licence permits

| Right | B | C | D |
|---|:--:|:--:|:--:|
| Read and use internally | ✅ | ✅ | ✅ |
| Build a tool for your own service use | ✖ | ✅ | ✅ |
| Distribute a product commercially | ✖ | ✖ | ✅ |
| Modify and extend | ✖ | ✅ | ✅ |
| Multiple product lines | ✖ | ✖ | ✅ |
| Sublicense to your customers | ✖ | ✖ | ✅ |
| Redistribute the reports | ✖ | ✖ | ✅ |
| Transfer the licence | ✖ | ✖ | with consent |

Tier D rights are internal-commercial unless separately negotiated for resale of the
research itself.

---

## 3. Confidentiality

**Mutual NDA required before Tier B.**

Confidential on both sides:

- You: the material, the method, the data.
- Me: your identity, your business, what you told me about your plans.

Specifics:

- **No reverse-engineering of the deliverables** to extract withheld data you did not
  license. This matters at Tier B, where data is deliberately absent.
- **No redistribution** below Tier D, including "for evaluation" or "to a contractor".
  Contractors needing access need their own licence or must be named in yours.
- **No publication** of the findings, in whole or part, without written agreement. If you
  want to publish, say so upfront and we will agree terms — I am not automatically
  opposed.
- Term: 5 years from delivery, except trade secrets which survive indefinitely.

---

## 4. Watermarking

Every delivered copy carries a per-recipient watermark — an unobtrusive marker encoded in
the files that identifies which licence it came from.

You are told this exists. It is not hidden, and it is not a threat; it simply means a
leaked copy can be traced, which protects both of us. Do not remove it, and do not pass
copies to third parties.

---

## 5. Source escrow (Tier C and D)

Available on request, so you are never stranded if I become unavailable.

- A third party holds the archive.
- Release triggers: my death or incapacity, or my failure to respond to support
  obligations for 60 days.
- You pay escrow costs.
- Escrow release grants the same rights as your licence tier. It does not expand them.

This exists to remove a legitimate objection. It does not give you the code early.

---

## 6. Warranties and limits

**What I warrant:**

- The material is what this package describes.
- The commitment hashes match the delivered content (`COMMITMENT.md` §5).
- Where a claim is uncertain, it is labelled as such in the reports.
- I have the right to license it.

**What I do not warrant:**

- That it works on every device or firmware revision.
- That it is free of error. It is research; the open-question register lists what is
  unknown.
- Fitness for a particular purpose.
- That using it is lawful in your jurisdiction — that is your determination (§7).

**Liability:** capped at the licence fee paid. No liability for consequential loss,
including bricked hardware, lost data or lost revenue.

---

## 7. Your responsibilities

- Obtain your own legal advice before use.
- Confirm you are authorised to service the devices you work on.
- Comply with data-protection law for any device data you handle.
- Do not alter IMEI or other regulated identifiers.
- Do not use the material to defeat locks on devices you do not own or are not authorised
  to service.
- Tell your customers that unlocking voids manufacturer warranty.

---

## 8. Termination

- **By you:** at any time. Refund is pro-rata for undelivered tiers only.
- **By me:** on material breach — unauthorised redistribution, reverse-engineering to
  extract withheld data, or use contrary to §7. No refund on termination for breach.
- On termination you delete all copies and confirm deletion in writing, except where a
  perpetual licence was granted.

---

## 9. Provenance acknowledgement

By licensing, you acknowledge:

- The material is an **independent reconstruction and behavioural specification**, not
  vendor source code (`SCOPE.md` §4).
- You will not represent it as vendor source code.
- The vendor's signing key is not included and cannot be obtained.

This acknowledgement is a condition of the licence, not a formality. Misrepresenting the
provenance creates liability that would otherwise not exist.

---

## 10. Next steps

1. Read `BRIEF.md` — 10 minutes.
2. Check one claim yourself: `FINDINGS/01-unlock-techniques.md` §1 — an hour.
3. Decide the tier you need.
4. NDA, then Tier B or straight to your target tier.
5. Delivery, verify the manifest, done.

If after step 2 you are not convinced, stop there. That is what step 2 is for.
