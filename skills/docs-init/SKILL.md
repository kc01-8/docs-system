---
name: docs-init
description: Install the living-docs system into the current repo — scaffold DOCS/ (ARCHITECTURE, MODULE_MAP, USER_MANUAL, CHANGELOG, ROADMAP, archive/) from templates, fill the agent docs by analyzing the codebase, and create/extend CLAUDE.md with project-specific hard rules. Use when setting up a new project or retrofitting the docs system onto an existing one.
---

# docs-init — install the docs system into this repo

You are instantiating a **living-docs system** whose whole value is accuracy. Generic filler is worse
than a TODO marker: never invent facts about the codebase — everything you write into ARCHITECTURE and
MODULE_MAP must be verified by reading the actual source. Where you can't determine something (domain
intent, remote URL, collaborator count), write an explicit `<!-- TODO(human): ... -->`.

Templates live in `${CLAUDE_PLUGIN_ROOT}/templates/`. Placeholders are `{{LIKE_THIS}}`; HTML comments
in the templates are generation guidance — follow them, then delete them from the output (keep the
ones explicitly meant to survive, e.g. the "Older entries" pointer and format-hint comments in
CHANGELOG/MODULE_MAP that say "update this in the same pass").

## Procedure

### 1. Pre-flight
- Must be a git repo (if not, ask before `git init`).
- If `DOCS/` already exists, stop and ask: update-in-place (fill gaps, preserve their content) or
  abort. Never overwrite existing docs wholesale.
- If `CLAUDE.md` exists, you will **merge into it** (step 5), not replace it.

### 2. Analyze the codebase (skip to step 3 for an empty/new repo)
Read enough to fill the agent docs truthfully:
- Language(s), runtime versions, package manifests, entry points, build system, test setup.
- Top-level directory structure and the *actual* dependency direction between parts (check imports).
- Singletons / module-level shared state / config loading — things that must be imported, not re-created.
- Conventions an agent would get wrong by default: time handling, units, ID formats, error style.
- Storage: databases, data dirs, caches; what's gitignored/per-machine.
- Quirks: TODO/FIXME/HACK comments, suspicious workarounds, platform-specific code.
- Public API per file (classes, key functions, signatures) for MODULE_MAP.

For a large codebase, be systematic per top-level directory; it's fine for first-pass MODULE_MAP
entries to be terse — terse and correct beats detailed and guessed.

### 3. Scaffold DOCS/
Create `DOCS/` and `DOCS/archive/` and instantiate every template from
`${CLAUDE_PLUGIN_ROOT}/templates/DOCS/` (drop the `.tmpl` suffix):
- **README.md** — fill project name/description; keep the maintenance rules verbatim.
- **ARCHITECTURE.md** — fill every section from your step-2 analysis. Empty repo: keep section
  skeleton with TODOs.
- **MODULE_MAP.md** — one entry per source file with public symbols. Empty repo: skeleton + format
  comment.
- **USER_MANUAL.md** — scaffold with Part structure; fill what's derivable (CLI flags, config
  options), TODO the domain theory (that's the human's voice). For a pure library with no end-user
  surface, ask whether to skip it (and remove its rows from README's tables if skipped).
- **CHANGELOG.md** — header verbatim (the 500-line archival rule is load-bearing); seed with today's
  "Docs system initialized" entry, using the real date.
- **ROADMAP.md** — claim convention verbatim; seed work areas from anything you found (TODOs, obvious
  gaps), else leave one empty area table.

If the repo root contains original design briefs / spec documents, propose moving them to
`DOCS/archive/` (don't move without confirming — they may be referenced elsewhere).

### 4. Migrate stray docs
If there's an existing README with architecture/usage content, or scattered `docs/`, `NOTES.md` etc.:
keep the root README as the short public-facing intro, move/merge the rest into the DOCS/ files, and
leave pointers. Confirm before deleting anything.

### 5. CLAUDE.md
From `${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md.tmpl`:
- **Hard rules must be project-specific**, derived from step 2 (dependency direction, singletons,
  conventions, threading rules). 3–6 rules. If you truly found none, write the section with a
  TODO(human) — do not pad with generic advice.
- Build/run/test block mirrors ARCHITECTURE §8.
- Solo project: trim the "Working together" section to the doc-maintenance bullet; keep the roadmap
  claim rule only if collaboration is plausible. Unknown remote: TODO.
- Existing CLAUDE.md: add the missing pieces (Read first, Hard rules, Working together) around what's
  already there; never duplicate or contradict existing instructions — reconcile, asking if unsure.

### 6. Finish
- Verify all `{{PLACEHOLDER}}`s are gone (grep for `{{`), guidance comments removed.
- Suggest (don't auto-run unless asked) a commit: `Install living-docs system (DOCS/ + CLAUDE.md)`.
- Report: files created, what was auto-filled vs. `TODO(human)` (list these explicitly — they're the
  user's punch list), and any migrations proposed in steps 3–4 still awaiting confirmation.
