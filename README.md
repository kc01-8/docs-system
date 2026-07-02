# docs-system

A drop-in **living-docs system** for any codebase, packaged as a Claude Code plugin. Install once,
then one command (`/docs-init`) sets up any project with the full documentation discipline:

- **`DOCS/ARCHITECTURE.md`** *(agent)* — structure, hard rules, invariants, conventions, quirks,
  build/run/test.
- **`DOCS/MODULE_MAP.md`** *(agent)* — per-file public API, so agents work without reading every file.
- **`DOCS/USER_MANUAL.md`** *(human)* — domain concepts and end-user behavior.
- **`DOCS/CHANGELOG.md`** — newest-first, capped at 500 lines with mandatory archival (move, never
  delete).
- **`DOCS/ROADMAP.md`** — planned work with a **claim convention** (Owner + branch, committed first)
  so collaborators never duplicate effort.
- **`DOCS/archive/`** — original design briefs + archived changelog chunks.
- **`CLAUDE.md`** — read-first pointers into DOCS/, 3–6 *project-specific* hard rules, and the core
  contract: **code change ⇒ doc change in the same commit.**

Extracted from the system developed on The M.P.D. project.

## Install (once, works in every project)

```bash
claude plugin marketplace add kc01-8/docs-system
claude plugin install docs-system@docs-system
```

Or from inside Claude Code: `/plugin` → browse → install `docs-system`.

## Use

| Command | What it does |
|---------|--------------|
| `/docs-init` | Scaffold `DOCS/` + `CLAUDE.md` in the current repo. Analyzes the codebase to fill ARCHITECTURE and MODULE_MAP truthfully; marks what it can't know as `TODO(human)`. Safe on existing repos (merges, never clobbers). |
| `/docs-check` | Staleness audit: MODULE_MAP vs actual symbols, CHANGELOG vs recent commits, roadmap claim hygiene, CLAUDE.md drift. Reports; fixes only when asked. |
| `/docs-archive` | When the changelog passes 500 lines, cut the oldest entries into `DOCS/archive/CHANGELOG-archive-NN.md` as a standalone commit. |

## The rules the system enforces

1. **Code change ⇒ doc change in the same pass** — public symbol changes update MODULE_MAP, behavior
   changes update ARCHITECTURE, and either gets a CHANGELOG entry.
2. **Agent docs stay terse and accurate** — stale entries are worse than none.
3. **Archived documents are never edited** — corrections go in the live docs.
4. **USER_MANUAL owns the domain; ARCHITECTURE/MODULE_MAP own the code.**
5. **CHANGELOG stays under 500 lines** — history is moved to archive chunks, never deleted.
6. **Claim roadmap items before starting** — Owner + branch, committed and pushed first.

## Repo layout

```
.claude-plugin/         plugin + marketplace manifests
skills/
  docs-init/SKILL.md    scaffold + generate
  docs-check/SKILL.md   staleness audit
  docs-archive/SKILL.md changelog chunking
templates/
  CLAUDE.md.tmpl
  DOCS/*.md.tmpl        the six doc templates
```

Improving the system: edit the templates/skills here and reinstall (or `claude plugin marketplace
update docs-system`) — every future `/docs-init` picks up the changes. Already-initialized projects
own their generated docs; changes here don't retro-apply to them.
