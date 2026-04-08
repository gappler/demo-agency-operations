---
title: Project Notes — Demo Agency Operations
version: 1
created: 2026-04-08
updated: 2026-04-08
description: Ongoing log of insights, methodology notes, and skills inventory for this project
---

# Project Notes

## Skills Inventory

### Superpowers Skills Invoked

| Skill | Purpose |
|-------|---------|
| `superpowers:brainstorming` | Pre-implementation design exploration for Resource Allocator, Capacity Planner, and other tools |
| `superpowers:writing-plans` | Created implementation plans in `docs/superpowers/plans/` (4 dated plan files) |
| `superpowers:executing-plans` | Multi-step execution of each tool build |
| `superpowers:verification-before-completion` | Verification steps before marking tools complete |
| `superpowers:requesting-code-review` | Review passes before completion |
| `frontend-design:frontend-design` | UI design for all 7 HTML tools — dark headers, card layouts, color-coded status indicators |

### Core Claude Code Tools Used

- **Read** — Reading `brand_definition.md`, existing tool files for data consistency, SOW/performance data
- **Write** — Creating all 7 HTML tools, tutorials, data files, process log
- **Edit** — Incremental modifications to `process_log.md`, `index.html`, and tool refinements
- **Bash/Git** — `git add`, `git commit` after each tool (7+ commits visible in history)
- **Glob** — File discovery across `tools/`, `tutorials/`, `docs/`
- **Grep** — Searching for data structures and implementations across files

### Agent / Sub-Agent Usage

- No parallel sub-agents dispatched — the build was sequential across 3 sessions (Apr 6-8, 2026)
- Work followed a structured pipeline: spec, plan, scaffold, data layer, render, polish, document, commit

### MCP Servers & External Tools

- None. All 7 tools are zero-dependency, self-contained HTML/CSS/JS files with no external API calls, no frameworks, no build steps, and no MCP server integrations.

### Skills Not Invoked

`subagent-driven-development`, `dispatching-parallel-agents`, `test-driven-development`, `systematic-debugging`, `using-git-worktrees`, `claude-api`

---

## Build Methodology Pattern (Reusable Reference)

The following pattern was used for each of the 7 tools built in this project. It can be reused as a repeatable workflow for future tool builds.

### Per-Tool Build Steps

1. **Brainstorming** — Invoke `superpowers:brainstorming` to explore design decisions (matrix orientation, interaction model, data granularity, filtering options)
2. **Planning** — Invoke `superpowers:writing-plans` to produce a step-by-step implementation plan saved to `docs/superpowers/plans/`
3. **Data alignment** — Read existing data files (`brand_definition.md`, prior tool files) to ensure consistent team rosters, client lists, and metric definitions
4. **HTML scaffold** — Write the tool file with header, stat cards, and layout structure
5. **Data layer** — Embed all JavaScript constants, utility functions, and sample data
6. **UI rendering** — Invoke `frontend-design:frontend-design` for polished, production-grade interface design
7. **Verification** — Invoke `superpowers:verification-before-completion` to confirm functionality before marking complete
8. **Documentation** — Write a tutorial in `tutorials/` and append an entry to `docs/process_log.md`
9. **Git commit** — Stage all changes and commit with a descriptive message

### Key Principles

- **Zero dependencies** — Every tool is a single self-contained HTML file that opens in any browser
- **Data consistency** — All tools share the same team roster, client list, and metric definitions from foundational data files
- **Sequential builds** — Each tool builds on the data and patterns established by prior tools
- **Document as you go** — Tutorials and process log entries are written immediately after each tool, not batched

---

## Insights Log

_Add entries below as new insights emerge. Newest first._

### 2026-04-08 — Initial inventory compiled

Cataloged all skills, tools, agents, and methodology used across 3 build sessions (Apr 6-8). No external dependencies or MCP servers were used. The brainstorming-then-planning pattern proved effective for consistent output quality across all 7 tools.
