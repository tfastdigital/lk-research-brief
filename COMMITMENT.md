# Commitment — proving the content exists without revealing it

This document explains the single mechanism that lets me demonstrate I hold a
substantial body of work without handing any of it over, and lets you verify later that
what you receive is exactly what was described now.

---

## 1. The idea

A cryptographic hash is a **one-way commitment**. Publishing the hash of a file you never
show proves three things and reveals nothing:

1. **You had the content** — you cannot produce a hash without the bytes.
2. **It is fixed** — you cannot later substitute different content without changing the
   hash.
3. **Nothing leaked** — the hash itself is useless for reconstructing the input.

Applied to a whole tree, this becomes a strong claim: *"I hold 4,793 files, and here is
the fingerprint of the complete manifest of their individual fingerprints."*

---

## 2. What is published

Two aggregate hashes:

```
MANIFEST-decompiled.sha256  (4,793 lines) = fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
MANIFEST-docs.sha256        (23 lines)    = c14059ca5bfc2432decb82bc278750d1c122d49b86f01f6ba73f2633731c8d58
```

Plus, for corroboration: the file counts and total byte sizes in `STATISTICS.md`.

## 3. What is NOT published

The manifests themselves. A manifest lists filenames, and filenames reveal structure —
which classes exist, how the client is organised, what the reports are called. That is
commercially sensitive. You get the aggregate hash now and the manifest at delivery.

---

## 4. How each manifest is built

Deterministic and reproducible. Exactly this, nothing more:

```powershell
# For each file, under a stable root:
#   <sha256 of file contents>  <path relative to root>
# then sort all lines ordinally, then hash the resulting file.

$root = "<analysis root>"

Get-ChildItem "$root\decompiled" -Recurse -File |
    ForEach-Object {
        "{0}  {1}" -f (Get-FileHash $_.FullName -Algorithm SHA256).Hash.ToLower(),
                      $_.FullName.Replace("$root\","")
    } |
    Sort-Object |
    Out-File MANIFEST-decompiled.sha256 -Encoding ASCII

Get-FileHash MANIFEST-decompiled.sha256 -Algorithm SHA256
# -> fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
```

Note the details that make this reproducible by a third party:

- SHA-256, lowercase hex
- exactly two spaces between hash and path
- relative paths, forward-slash normalised by the source tree
- **ordinal** sort of the assembled lines
- ASCII encoding, so line endings cannot drift

Conventions are stated so you can rebuild the manifest independently and get the same
aggregate. If you cannot, the content differs.

---

## 5. Verification at delivery

At delivery you receive the archive plus the manifest. Do this **before** reading
anything:

```powershell
# 1. Recompute per-file hashes over the delivered archive
Get-ChildItem .\delivered -Recurse -File |
    ForEach-Object {
        "{0}  {1}" -f (Get-FileHash $_.FullName -Algorithm SHA256).Hash.ToLower(),
                      $_.FullName.Replace((Resolve-Path .\delivered).Path + "\","")
    } |
    Sort-Object |
    Out-File received.sha256 -Encoding ASCII

# 2. The manifest must be byte-identical
Compare-Object (Get-Content .\MANIFEST-decompiled.sha256) (Get-Content .\received.sha256)
#    no output = identical

# 3. And its aggregate must equal the published commitment
Get-FileHash .\received.sha256 -Algorithm SHA256
#    must equal fe4d29db5158e5c41173f02371e4c452e357412e1c9f61d75695c316d0f5fdc6
```

If step 3 matches, then **every one of the 4,793 files you received is byte-identical to
what existed when this brief was written**. Not "similar". Identical.

That is a stronger guarantee than reading a report and believing it.

---

## 6. What this does and does not prove

| Proves | Does not prove |
|---|---|
| The content existed at this date | That the content is *useful to you* — only you can judge that |
| Nothing was substituted since | That every claim inside it is correct — see the confidence labels |
| The delivery matches the description | That you have rights to use it — that is the licence |
| The volume is real, not asserted | — |

Be clear on the second row. A hash proves **integrity**, not **accuracy**. Accuracy is
addressed by the confidence-labelling discipline: every claim in the reports is tagged
verified, public-source, inferred, or unknown, and seven open questions are listed
openly rather than hidden.

---

## 7. Why not just show a sample?

You can have a sample — `FINDINGS/` contains worked examples that demonstrate method and
depth. What a sample cannot do is prove **volume**. Anyone can write one convincing page.

The commitment solves the volume problem specifically: it is a claim about 4,793 files
that costs nothing to state, cannot be faked, and can be settled definitively later. The
sample proves quality; the commitment proves scale. You need both, and they are different
mechanisms.

---

## 8. If you want independent verification now

Three options, none of which require me to disclose anything:

1. **Ask for a partial manifest.** I can publish the manifest for a *subset* you choose —
   for example "the 23 report files". That reveals the report filenames only (already
   listed in `SCOPE.md`), and lets you verify a hash chain end to end today.
2. **Ask for a signed statement** binding the commitment to a date, for your records.
3. **Bring your own firmware.** Give me one dump and I will show you what the research
   predicts about it before I see it. That is a live test of the method, not the archive.

Option 3 is the strongest available proof and the one I would choose in your position.
