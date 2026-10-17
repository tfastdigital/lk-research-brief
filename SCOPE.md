# Scope and deliverables

What is in each tier, what is excluded, and the provenance of the material.

---

## 1. Tier structure

### Tier A — Brief (this package)

Free. What you are reading, plus `STATISTICS.md`, `COMMITMENT.md`, and two worked
findings in `FINDINGS/`.

Purpose: enough to judge quality and decide whether to proceed.

### Tier B — Technical pack

All **23 reports**. The complete written analysis, with no patch data.

| Included | |
|---|---|
| Architecture report — component roles, service boundaries, data flow | ✅ |
| Obfuscator analysis — VM dispatch reconstruction, trampolines, limits | ✅ |
| Bootloader research — both techniques, method and failure modes | ✅ |
| Acquisition manual — what to pull from hardware, validation gates | ✅ |
| Build playbook — discovery techniques, architecture, code, test matrix | ✅ |
| Partition reference — 40+ partitions, roles, sizes, safety class | ✅ |
| Protocol analysis — recovered formats and method | ✅ |
| Backend contract — action catalogue, envelope shapes | ✅ |
| Confidence labels and open-question register | ✅ |

### Tier C — Implementation licence

Tier B **plus the extracted data**. This is the tier that saves real time.

| Added | |
|---|---|
| Device profile definitions — per-model patch site data | ✅ |
| Complete marker string sets per profile | ✅ |
| Scoring weights and thresholds | ✅ |
| Partition offset tables per device family | ✅ |
| Certificate-block layouts and bypass specifications | ✅ |
| Full backend action-to-token mapping | ✅ |
| Engine data model and catalogue schema | ✅ |
| **Extraction toolkit** — the scanner/validator scripts | ✅ |

**Commercial right:** build one product line on top of it.

### Tier D — Full assignment

Everything, plus the analysis tree and broad rights.

| Added | |
|---|---|
| Decompiled reconstruction (4,793 files, 1.26 GB) | ✅ |
| VM disassembly dumps (5,573 files) | ✅ |
| Packet captures and runtime dumps | ✅ |
| All 135 analysis scripts | ✅ |
| Manifests for verification | ✅ |
| **Commercial right:** unlimited product lines, right to extend and sublicense internally | ✅ |

---

## 2. Explicitly excluded from every tier

| Excluded | Reason |
|---|---|
| Vendor unlock-token signing key | **Not held.** Cannot be supplied by anyone. |
| Vendor source code | Not held — this is independent reconstruction |
| Redistribution rights below Tier D | Licensing term |
| Any pre-built redistributable end-user tool | This is a specification, not a product |
| Live credentials, session tokens | Deleted before packaging |
| Real device identifiers (IMEI/serial/HWID) | Redacted; no device data included |
| Third-party AGPL implementation code | Not vendored — documented by reference only |

> **The signing key line is not a negotiating position.** If a device's bootloader
> verifies a vendor signature, no amount of research produces that key. What the research
> *does* give you is the ability to determine which devices require one — which is the
> commercially useful part.

---

## 3. Delivery

| Item | Format |
|---|---|
| Reports | Markdown, 23 files |
| Data tables | Markdown + machine-readable where applicable |
| Scripts | Python / PowerShell, runnable |
| Analysis tree (Tier D) | Archive + SHA-256 manifest |
| Verification | Manifest comparison procedure (`COMMITMENT.md` §5) |

Delivered by private repository grant or encrypted archive, per your preference.
Every copy carries a per-recipient watermark.

---

## 4. Provenance — read this before signing

Stated precisely, because it determines what you can safely do with the material.

**What this is:** an independent technical reconstruction and behavioural specification,
produced by analysing a shipped application and by studying publicly available device
protocols and file formats.

**What this is not:** the vendor's source code. No vendor source was obtained. The
decompiled tree is output from a general-purpose decompiler applied to a legally obtained
binary, together with reconstruction work to make it readable.

**Why the distinction matters legally:**

- Observation of behaviour and interface analysis is a recognised interoperability
  practice in most jurisdictions.
- Possessing and reselling another party's source code is a different, much worse
  position.
- Clean-room reconstruction from observed behaviour — which is what this is — is the
  standard method used for compatible implementations.

**What you should do:**

- Obtain your own legal advice; this document is not legal advice.
- Treat the *methods and findings* as your primary asset, not the decompiled tree.
- If you only need to build a compatible tool, Tier B or C is sufficient and carries less
  exposure than Tier D.
- Do not represent the material as the vendor's source code. It is not, and a claim
  otherwise creates liability you do not currently have.

**What was studied:** a commercially distributed client application, obtained and
analysed for interoperability research. Publicly available protocol and file-format
documentation. One public open-source implementation, analysed for mechanism only — no
code from it is included in any tier.

---

## 5. Ethical and legal constraints on use

Included because a buyer who does not know these will create problems for both of us.

| Constraint | Detail |
|---|---|
| **Authorisation** | You must be authorised to service the devices you work on. The material is for repair, interoperability and security research |
| **No IMEI alteration** | The research touches partitions that hold IMEI. Altering IMEI is illegal in most jurisdictions and is not a supported use |
| **No bypass for theft** | Do not use this to defeat locks on devices you do not own or are not authorised to service |
| **Data protection** | Any device data you handle is subject to data-protection law |
| **Warranty** | Unlocking voids manufacturer warranty. Your customers must be told |
| **Brick risk** | Wrong patch = dead device. The material documents this; it does not eliminate it |

I will decline a sale where the stated use is inconsistent with these. That is not
fussiness — a buyer who intends to misuse this creates liability that follows the
material back.

---

## 6. Support and handover

| Tier | Support |
|---|---|
| A | None |
| B | Written questions for 30 days |
| C | 30 days written + one 2-hour technical session |
| D | 30 days written + two technical sessions + handover walkthrough of the tree |

Handover for D includes: how the tree is organised, how to re-run the analysis, which
scripts do what, and what not to touch without a backup.

No ongoing retainer is offered. The intent is that you do not need me afterwards.
