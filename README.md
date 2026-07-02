# docs-system — Living Documentation for AI Coding Agents (Claude Code Plugin)

**Auto-generate and maintain agent-readable documentation for any codebase.** docs-system is a
[Claude Code](https://claude.com/claude-code) plugin that installs a complete documentation system —
architecture docs, per-file module map, changelog, roadmap, and a `CLAUDE.md` with project-specific
hard rules — into any repository with a single command, then keeps it honest with staleness audits.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-d97757.svg)](https://docs.claude.com/en/docs/claude-code/plugins)

Install once at user level → run `/docs-init` in any project → get documentation that AI agents and
human collaborators can actually rely on.

## Installation

```bash
claude plugin marketplace add kc01-8/docs-system
claude plugin install docs-system@docs-system
```

Or from inside Claude Code: `/plugin` → browse → install `docs-system`. Installed at user scope, it
works in every repository on your machine.

## Why agent-readable docs?

AI coding assistants (Claude Code, and agentic coding tools generally) burn most of their context
window re-discovering your codebase: the layer structure, the singletons, the unit conventions, the
quirks. A good `CLAUDE.md` helps, but it goes stale, and stale documentation is worse than none,
because an agent will confidently act on it.

docs-system solves both halves of the problem:

1. **Generation** — `/docs-init` analyzes your codebase and writes truthful docs (never invented:
   anything unverifiable becomes an explicit `TODO(human)`).
2. **Maintenance** — the generated rules make doc updates part of every commit
   (**code change ⇒ doc change in the same commit**), and `/docs-check` audits the contract after
   the fact.

## What you get in every project

| File | Audience | Purpose |
|------|----------|---------|
| `CLAUDE.md` | agent | Read-first pointers + 3–6 *project-specific* hard rules (dependency direction, singletons, conventions) |
| `DOCS/ARCHITECTURE.md` | agent | Layers, invariants, data flow, storage, known quirks, build/run/test |
| `DOCS/MODULE_MAP.md` | agent | Per-file public API — work on the codebase without reading every source file |
| `DOCS/USER_MANUAL.md` | human | Domain concepts and end-user behavior |
| `DOCS/CHANGELOG.md` | both | Newest-first, capped at 500 lines with mandatory archival (move, never delete) |
| `DOCS/ROADMAP.md` | both | Planned work with a **claim convention** (Owner + branch, committed first) so collaborators never duplicate effort |
| `DOCS/archive/` | both | Original design briefs + archived changelog chunks — history preserved forever |

## Usage — three slash commands

| Command | What it does |
|---------|--------------|
| `/docs-init` | Scaffold `DOCS/` + `CLAUDE.md` in the current repo. Analyzes the codebase to fill ARCHITECTURE and MODULE_MAP truthfully; marks what it can't know as `TODO(human)`. Safe on existing repos — merges, never clobbers. |
| `/docs-init --local` | Same, but the generated docs are **gitignored** — private, per-machine documentation for repos whose upstream shouldn't carry agent docs. Off by default; collaborators and CI won't see the docs. |
| `/docs-check` | Staleness audit: MODULE_MAP vs actual public symbols, CHANGELOG vs recent git history, roadmap claim hygiene, CLAUDE.md drift. Reports findings; fixes only when asked. |
| `/docs-archive` | When the changelog passes 500 lines, cut the oldest entries into `DOCS/archive/CHANGELOG-archive-NN.md` as a standalone commit. |

## The six rules the system enforces

1. **Code change ⇒ doc change in the same pass** — public symbol changes update MODULE_MAP, behavior
   changes update ARCHITECTURE, and either gets a CHANGELOG entry.
2. **Agent docs stay terse and accurate** — stale entries are worse than none.
3. **Archived documents are never edited** — corrections go in the live docs.
4. **USER_MANUAL owns the domain; ARCHITECTURE/MODULE_MAP own the code.**
5. **CHANGELOG stays under 500 lines** — history is moved to archive chunks, never deleted.
6. **Claim roadmap items before starting** — Owner + branch, committed and pushed first, so two
   people (or two agents) never build the same thing twice.

## FAQ

**Does this work on existing projects, or only new ones?**
Both. On an existing repo, `/docs-init` merges around whatever `CLAUDE.md` and docs you already have,
migrates stray notes into `DOCS/`, and offers to archive original design briefs — it never
overwrites your content.

**How is this different from just writing a CLAUDE.md?**
A `CLAUDE.md` is one file with no upkeep story. docs-system separates *always-loaded rules*
(CLAUDE.md) from *look-up references* (ARCHITECTURE, MODULE_MAP), gives humans their own manual, and
— the part that matters — makes maintenance enforceable and auditable (`/docs-check`).

**Is it Claude Code only?**
The skills are Claude Code plugin skills, but the generated output is plain Markdown with a
conventional layout — useful to any AI coding agent (or human) that reads the repo.

**Can two people / two agents collaborate with it?**
That's what it was built for: the ROADMAP claim convention and the same-commit doc rule came out of a
two-person, two-Claude-Code workflow on a real project.

## Repository layout

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

## License

[MIT](LICENSE) — use it anywhere, including commercial projects.

---

*Keywords: Claude Code plugin, CLAUDE.md generator, AI agent documentation, living documentation,
docs-as-code, documentation automation, agentic coding, architecture documentation, module map,
changelog management, AI-assisted software development.*
