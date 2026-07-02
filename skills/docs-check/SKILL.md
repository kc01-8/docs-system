---
name: docs-check
description: Audit the repo's DOCS/ system for staleness — MODULE_MAP vs actual public symbols, ARCHITECTURE vs current structure, CHANGELOG length and coverage of recent commits, ROADMAP claim hygiene, CLAUDE.md drift. Reports findings; only fixes when asked. Use periodically or before a release/handoff.
---

# docs-check — staleness audit

The docs system's contract is "code change ⇒ doc change in the same pass". This skill verifies that
contract after the fact. **Report findings; do not edit docs unless the user asked for fixes**
(e.g. `/docs-check --fix`). If `DOCS/` doesn't exist, say so and point at `/docs-init`.

Run these checks, collecting findings as you go:

## 1. MODULE_MAP vs reality
- List source files (per the project's layout; respect .gitignore). Flag files with public symbols
  that have **no MODULE_MAP entry**, and MODULE_MAP entries whose **file no longer exists**.
- For entries touched by recent commits (see §3), spot-check that named classes/functions/signatures
  still match the source. Don't re-verify the whole map every run — prioritize recently changed files.

## 2. ARCHITECTURE vs reality
- Do the layer/structure table and dependency rules still match the tree? Any new top-level dirs?
- Do the build/run/test commands still work-look right (files referenced exist, script names valid)?
  Run cheap offline gates if available.
- Known Quirks: any resolved (still documented) or new (TODO/FIXME/HACK added since) quirks?

## 3. CHANGELOG
- **Line count vs 500** — if over, flag for `/docs-archive`.
- Newest-first ordering intact; dates sane.
- Walk `git log` since the newest CHANGELOG entry's date: flag substantive code commits (not
  formatting/docs-only) with **no corresponding entry**.

## 4. ROADMAP hygiene
- 🚧 items: does the named branch exist / have recent commits? Stale claims (no activity for weeks) →
  flag for release back to 🔲.
- ✅ items: should have moved to CHANGELOG — flag lingerers.
- Shipped features still listed as 🔲 (cross-check CHANGELOG).

## 5. CLAUDE.md drift
- Build/run/test block still matches ARCHITECTURE §8.
- Hard rules still true (e.g. named singletons/modules still exist).
- Doc pointer list matches the actual files in DOCS/.

## Report
Group findings by severity:
- **Wrong** (doc says something false — worse than missing): fix these first.
- **Missing** (code/commits with no doc trace).
- **Housekeeping** (changelog length, stale claims, ordering).

For each finding: file, what's wrong, and the concrete edit that would fix it. End with a one-line
verdict (clean / N findings). If asked to fix: apply edits per the maintenance rules in
`DOCS/README.md`, newest-first in CHANGELOG, and summarize what changed.
