---
title: "Tutorial: Building the Resource Allocator"
version: 1.0
created: 2026-04-07
tool: tools/resource_allocator.html
---

# Tutorial: Building the Resource Allocator

Step-by-step guide to replicating the interactive resource allocation matrix for Sidebar Creative.

---

## Step 1: Brainstorm the Design

**Prompt:**
> Build a resource allocator for Sidebar Creative. Read the team roster and project data. Show each team member's current capacity — hours allocated vs. available — with visual indicators for overloaded (red) and underutilized (green) staff. Let me drag or reassign team members across projects and see capacity update in real time. Output as an interactive HTML file.

### Key Design Decisions

1. **Matrix orientation: Projects as rows, team as columns**
   - The user's initial instinct was a traditional team-first matrix, but after discussion, the flipped orientation was chosen because it mirrors the Monday resourcing meeting flow: walk through projects one by one, check staffing, adjust hours.
   - With 12 projects vs. 33 team members, projects-as-rows produces fewer rows to scan.

2. **Click-to-edit over drag-and-drop**
   - The user identified this as a "working document" — something kept open and actively adjusted during meetings. Click-to-edit is faster for making many small adjustments than drag-and-drop choreography.

3. **Soft over-allocation warning (not hard constraint)**
   - Real agencies sometimes need to over-allocate temporarily. The tool shows an inline warning when pushing someone past 115% and suggests underutilized alternatives, but lets you confirm and proceed.

4. **Week/Month toggle**
   - Weekly is the default (the working unit for day-to-day resourcing). Monthly multiplies by 4.33 to show SOW-aligned totals for contract-level reviews.

5. **Department filter tabs**
   - With 33 billable staff, showing everyone at once makes the table very wide. Department tabs (Design/Dev/Strategy/Content/Account-PM) keep the columns manageable.

## Step 2: Data Preparation

### Sources
- `brand/brand_definition.md` — Team roster (names, roles, departments, capacity, billable targets, current load)
- `data/client_sow_terms.md` — SOW hour allocations by discipline per client

### Deriving Weekly Allocations

The SOW data defines monthly hours per discipline per client. To get weekly hours:
1. Take monthly discipline hours (e.g., Evergreen Design: 80 hrs/month)
2. Divide by 4.33 to get weekly base
3. Map to the primary team member for that discipline (from client details)
4. Scale proportionally so each person's total allocations match their known `currentLoad`

This ensures the matrix correctly identifies the 5 overloaded (>115%) and 4 underutilized (<70%) staff members from the brand definition.

## Step 3: Build the HTML Scaffold

Create `tools/resource_allocator.html` with:
- Dark header matching the project timeline dashboard (#1a1a1a background)
- 4 summary stat cards (Overloaded, Underutilized, Total Hrs/Week, Avg Utilization)
- Department filter tab bar with Week/Month toggle
- Empty matrix container (`<table>` with thead/tbody/tfoot)

All CSS is inlined in a `<style>` tag. No external dependencies.

## Step 4: Embed the Data

Add a `<script>` block with three data structures:
- `TEAM` array — 33 billable staff (excluding Leadership and Ops/Admin)
- `PROJECTS` array — 12 engagements (8 retainers + 4 project-based)
- `allocations` object — nested `allocations[projectId][personId] = weeklyHours`

Plus utility functions: `getPersonTotal()`, `getUtilization()`, `getStatusColor()`, `formatMoney()`.

## Step 5: Render the Matrix

The `renderMatrix()` function rebuilds the full table on every change:
- **thead:** Team member columns with abbreviated name, role, mini capacity bar, utilization %
- **tbody:** Project rows grouped by type (Retainers, Project-Based), each with hour cells
- **tfoot:** Per-person total allocated vs. billable target, color-coded

Hour cells show the value with a tinted background matching the person's utilization color. Empty cells show "—" with a hover state.

## Step 6: Add Click-to-Edit

Event delegation on the table: clicking an `.hour-cell` replaces its content with a number input. Enter/Tab confirms the edit, Escape cancels. On confirm, the allocation is updated and `renderMatrix()` is called, which recalculates all totals, capacity bars, and stat cards.

## Step 7: Add Over-Allocation Warning

When an edit would push someone past 115%, instead of applying immediately:
1. Show an inline popover below the cell
2. Display the projected utilization percentage
3. Suggest up to 2 underutilized teammates in the same department
4. Offer Confirm/Cancel buttons

The popover repositions itself if it would overflow the viewport.

## Step 8: Polish

- Close warnings on scroll (prevents stale popovers)
- Debounced resize handler (re-renders on window resize)
- Viewport-safe warning positioning

## Final Output

`tools/resource_allocator.html` — a single self-contained HTML file with zero dependencies. Open in any modern browser.
