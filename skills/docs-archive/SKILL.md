---
name: docs-archive
description: Archive the oldest DOCS/CHANGELOG.md entries into DOCS/archive/CHANGELOG-archive-NN.md when the changelog exceeds 500 lines. Moves history, never deletes it; done as its own commit. Use when /docs-check flags the changelog as over the cap.
---

# docs-archive — chunk the changelog

Enforces the CHANGELOG archival rule: the live file stays under **500 lines**; history is **moved,
never deleted**; the cut is **its own commit**.

## Procedure

1. Count lines in `DOCS/CHANGELOG.md`. If ≤ 500, report the count and stop — nothing to do.
2. Choose the cut point: keep the header block and roughly the most recent ~6 months of entries
   (cut on an entry boundary, never mid-entry). If 6 months of entries still exceeds ~450 lines,
   keep the newest ~450 lines' worth of whole entries instead.
3. Determine the next archive number: existing `DOCS/archive/CHANGELOG-archive-NN.md` files,
   zero-padded, incrementing (`-01`, `-02`, …).
4. Create the new archive file with a small header, then the moved entries **verbatim, in their
   original order**:
   ```markdown
   # Change Log — archive NN ({{oldest date}} … {{newest date}})

   > Entries moved from DOCS/CHANGELOG.md on {{today}}. Do not edit — historical record.
   > Newer entries: [../CHANGELOG.md](../CHANGELOG.md){{, older: CHANGELOG-archive-(NN-1).md if any}}
   ```
5. In the live CHANGELOG, remove the moved entries and update the pointer line under the header to
   list the archives, newest archive first:
   `Older entries: [archive/CHANGELOG-archive-NN.md](archive/CHANGELOG-archive-NN.md), …`
   (replacing the `<!-- Older entries: none archived yet. -->` comment if present).
6. Verify: live file now well under 500 lines; no entry lost or duplicated (the moved entries appear
   exactly once, in the archive); links resolve.
7. Commit **just these two files** as its own commit, message like
   `Archive CHANGELOG entries {{range}} to archive-NN` — ask first if the user hasn't already
   authorized committing.
