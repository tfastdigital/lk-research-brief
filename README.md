# MediaTek LK & META Research | Buyer Demonstration

**Independent reverse-engineering research portfolio — TFast Digital Agency**

> **DEMO / CAPABILITY PREVIEW ONLY.** This repository demonstrates the scope, methodology, and selected findings of a larger private research project. It is **not** a source-code release, ready-to-run unlocking tool, or complete technical delivery.

## Why this repository exists

Prospective research and development partners should be able to evaluate the work **before** discussing a commercial licence. This preview provides a transparent research summary, reproducible measurement methods, selected worked examples, known limitations, and the proposed scope of paid deliverables.

**[Start with the 10-minute capability brief](BRIEF.md)** · **[View measured research statistics](STATISTICS.md)** · **[Read sample findings](FINDINGS/01-unlock-techniques.md)**

## Research snapshot

| Research area | Reported scope |
| --- | ---: |
| Decompiled reconstruction files analysed | 4,793 |
| VM disassembly dumps | 5,573 |
| Analysis scripts | 135 |
| Technical reports | 23 |
| Functional methods catalogued | ~700 |
| Backend actions identified | 38 |
| Device profiles documented | 5 |

*These figures are documented by the project in [STATISTICS.md](STATISTICS.md). The underlying private archive is not included here, so prospective buyers should verify archive contents during due diligence.*

## What the demonstration shows

- **LK bootloader research:** comparison of two distinct lock-verification designs, investigative methodology, and documented failure modes.
- **MediaTek META research:** factory-runtime architecture and protocol-analysis methodology.
- **Client application analysis:** research into an obfuscated commercial servicing client, including method inventories, component boundaries, and backend API behaviour.
- **Evidence and limits:** reproducible measurement instructions, sample technical findings, uncertainty labels, and explicitly withheld implementation data.

This is an **independent behavioural reconstruction and technical specification**, **not original vendor source code**. The complete research is not a finished, compilable commercial application.

## Explore the buyer demonstration

| Document | What it helps you evaluate |
| --- | --- |
| [BRIEF.md](BRIEF.md) | Research objective, technical scope, findings, and known gaps |
| [STATISTICS.md](STATISTICS.md) | Counts, measurement methods, and verification claims |
| [FINDINGS/01-unlock-techniques.md](FINDINGS/01-unlock-techniques.md) | Sample LK research and distinctions between verification models |
| [FINDINGS/02-protocol-recovery.md](FINDINGS/02-protocol-recovery.md) | Worked example of analysing a binary protocol |
| [COMMITMENT.md](COMMITMENT.md) | Hash-based archive integrity verification procedure |
| [SCOPE.md](SCOPE.md) | What is included and excluded at each commercial tier |
| [TERMS.md](TERMS.md) | Proposed licensing terms and buyer responsibilities |

## Available research licensing

| Tier | Scope | Access |
| --- | --- | --- |
| **A — Public brief** | This buyer demonstration and sample findings | Free preview |
| **B — Technical pack** | 23 research reports, excluding protected implementation data | Commercial licence / NDA |
| **C — Implementation licence** | Tier B plus extracted data and research toolkit | Commercial licence / NDA |
| **D — Full research assignment** | Analysis tree, scripts, and broader negotiated rights | Commercial licence / NDA |

See [SCOPE.md](SCOPE.md) and [TERMS.md](TERMS.md) for precise entitlements. **Do not assume rights to redistribute or commercially deploy from the preview.**

## How buyers can verify the work

1. Review the [sample findings](FINDINGS/01-unlock-techniques.md) and check the claims against independent evidence.
2. Examine the measurement methodology in [STATISTICS.md](STATISTICS.md).
3. Request a private technical review and confirm the licence tier.
4. At delivery, compare the archive with the published hash commitments following [COMMITMENT.md](COMMITMENT.md).

**Hash commitments establish integrity of a subsequently supplied archive when verified; they do not independently establish research accuracy, ownership, legal rights, or the original date of creation.** A buyer should conduct technical and legal due diligence.

## Known limitations

Some protocol operations remain unidentified; device support varies by firmware and SoC; the newest generations have not all been hardware-tested. No vendor signing keys, live credentials, original vendor source code, or ready-to-run end-user tool are supplied. See [BRIEF.md](BRIEF.md) for the open questions.

## Commercial inquiries

**TFast Digital Agency**

Website: https://tfastdigital.com

For licensing, research collaboration, or a private technical review, contact TFast Digital Agency through its official website. Pricing is scoped to the requested tier and delivery rights.

---

**Responsible use:** Research is intended for authorised repair, interoperability, and security investigation. It is not an authorisation to service third-party devices without consent. No guarantee of compatibility with any particular device or firmware is made.
