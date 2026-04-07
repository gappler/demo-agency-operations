---
title: Resource Allocator — Design Spec
version: 1.0
created: 2026-04-07
updated: 2026-04-07
status: approved
tool: tools/resource_allocator.html
parent: brand/brand_definition.md
---

# Resource Allocator — Design Spec

## Purpose

Replace Sidebar Creative's blind resource allocation (hallway conversations, gut feel) with an interactive matrix showing who's on what project, for how many hours, and where capacity problems exist. Designed to mirror the Monday resourcing meeting flow: walk through projects one by one, check staffing, adjust, then review aggregate capacity.

## Layout

### Matrix Orientation

- **Rows:** 12 engagements — 8 retainer clients, 4 project-based — grouped by type (retainers first, then project-based)
- **Columns:** Team members, filtered by department tab
- **Cells:** Weekly hours allocated per person per project. Click to edit.

### Sticky Elements

- **Left column (project info):** Pinned with `position: sticky; left: 0`. Always visible during horizontal scroll. Shows project name, monthly retainer or total budget, and health indicator dot (green/yellow/red).
- **Footer row (capacity totals):** Pinned with `position: sticky; bottom: 0`. Shows each person's total allocated hours vs. billable target, color-coded by utilization status. Always visible during vertical scroll.

### Department Filter Tabs

Horizontal tab bar above the matrix: All Depts | Design (12) | Development (8) | Strategy (4) | Content (3) | Account/PM (6). Selecting a department shows only that department's team members as columns. "All Depts" shows everyone (horizontally scrollable).

### Week/Month Toggle

Toggle in the tab bar area, right-aligned. Default: **Weekly** (the working unit for resourcing meetings). Monthly view multiplies weekly hours by 4.33 and shows SOW-aligned totals for contract-level review.

### Summary Stat Cards

Four cards above the matrix:
1. **Overloaded** — count of team members >115% utilization (red)
2. **Underutilized** — count of team members <70% utilization (green)
3. **Total Hrs/Week** — sum of all allocated hours across the matrix
4. **Avg Utilization** — mean utilization percentage across all billable staff

## Column Headers

Each team member column header contains:
- Abbreviated name (e.g., "Nico S.")
- Role label (e.g., "Sr. Designer") in smaller text
- Mini capacity bar (40px wide, color-coded)
- Utilization percentage

## Row Content

### Project Left Column
- Project name (bold, 13px)
- Monthly retainer amount or total project budget
- Health indicator: colored dot + label (Healthy / At Risk / Critical for retainers; % burned + status for projects)

### Hour Cells
- **Populated cells:** Show integer hours, bold. Background tinted to match the person's current utilization color (red/gray/green at low opacity).
- **Empty cells:** Show "—" in light gray. On hover, show a subtle "+" icon to indicate the cell is assignable.
- **Click to edit:** Clicking a cell activates an inline input field. Type a number, press Enter or Tab to confirm, Escape to cancel. The cell value updates and all dependent calculations (row totals, column totals, capacity bars, stat cards) recalculate instantly.

### Row Totals
Rightmost column: sum of hours allocated to that project across the currently visible department. Updates in real time.

## Interaction: Over-Allocation Warning

When an edit would push a team member's total allocated hours past 115% of their billable target:

1. A small inline tooltip/popover appears near the edited cell
2. Shows: "[Name] will be at [X]% — confirm?" with Confirm and Cancel buttons
3. Below the warning, suggests up to 2 underutilized alternatives in the same department: "Available: [Name] ([Y]% utilized, [Z] hrs free)"
4. Confirming applies the edit. Canceling reverts to the previous value.

This is a soft constraint — overallocation is allowed but acknowledged.

## Visual Design

Matches the existing project timeline dashboard conventions:

- **Header:** Dark bar (#1a1a1a) with white text, "Sidebar Creative" left-aligned, current date right-aligned
- **Background:** #f5f5f5
- **Cards/table:** White (#fff) with subtle shadow (`0 1px 3px rgba(0,0,0,.08)`)
- **Typography:** System sans-serif stack (-apple-system, BlinkMacSystemFont, Segoe UI, Roboto), 13px base for table content
- **Status colors:**
  - Red (#dc3545): Overloaded (>115% utilization)
  - Green (#28a745): Underutilized (<70% utilization)
  - Yellow (#ffc107): Warning range or at-risk projects
  - Gray (#555): Normal range (70–115%)
- **Cell tints:** Status color at 10–15% opacity for background, 30% for border
- **Spacing:** 12px cell padding, 8px gaps between filter tabs

## Data Sources

### Team Members (37 billable staff)

From `brand/brand_definition.md` Team Roster. Each person has:
- Name, role, department
- Weekly capacity (40 hrs for all)
- Billable target (varies: 16 hrs for partners, 30 hrs for strategy, 32 hrs for account/PM, 34 hrs for design/dev/content)
- Current load (weekly hours)

Leadership (4) and Operations/Admin (3) are excluded from the matrix — they are non-billable or have minimal billable targets.

### Projects (12 engagements)

From `brand/brand_definition.md` Active Client Portfolio and `data/client_sow_terms.md`. Each engagement has:
- Name, type (retainer/project), monthly value
- Health status (green/yellow/red)
- SOW hour allocations by discipline

### Hour Allocation Mapping

SOW terms define monthly hours per discipline per client (e.g., Evergreen: Design 80 hrs/month). These are divided by 4.33 to get weekly hours. Each client's detail section in `brand/brand_definition.md` lists a primary team (e.g., "Sophie (AM), Nico (design lead), Owen (dev lead), Celeste (strategy)"). The primary team member for each discipline gets the SOW hours for that discipline. Where the current load data implies additional team members are contributing, hours are split proportionally to match the known current load totals.

## Technical Implementation

- **Single self-contained HTML file** — all CSS and vanilla JavaScript inlined, zero external dependencies
- **Data embedded as JavaScript objects** — team roster, project list, and allocation matrix
- **No build step, no framework** — consistent with the project timeline dashboard
- **State is in-memory only** — edits persist during the session but not across page reloads (this is a planning/meeting tool, not a database)

## Out of Scope

- Persistent storage / database backend
- Multi-user real-time collaboration
- Integration with Harvest, Asana, or other external tools
- Historical tracking of allocation changes
- Drag-and-drop (click-to-edit covers the use case more efficiently)
- Non-billable staff in the matrix (Leadership, Ops/Admin)
