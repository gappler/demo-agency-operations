---
title: "Tutorial 03: Capacity Planner — Scaffold and Data Layer"
tool: capacity_planner
tasks: 1-2
version: 1.0
created: 2026-04-07
---

# Tutorial 03: Capacity Planner — Scaffold and Data Layer

This tutorial documents how to build the HTML scaffold and data layer for a capacity planner tool for a creative agency. It covers Tasks 1 and 2 of the full capacity planner build.

---

## What You're Building

A self-contained HTML file at `tools/capacity_planner.html` that provides:
- Visual chrome matching the existing resource allocator tool
- Four stat cards for key capacity metrics
- A three-panel layout for forecast, projects, and pipeline risks
- A complete JavaScript data layer with team roster, projects, allocations, and forecast utility functions

---

## Step 1: Read Existing Tool for Data

Before writing any code, read `tools/resource_allocator.html` to copy the exact TEAM array and allocations object. This ensures data consistency across tools and avoids invented numbers.

**Prompt used (paraphrased):**
> Read tools/resource_allocator.html to extract the TEAM array and allocations object.

Key data extracted:
- 33 billable staff across 5 departments (account, design, development, strategy, content)
- 12 projects (8 retainers + 4 project-based)
- Allocation matrix with weekly hours per person per project

**Rationale:** Single source of truth. If you invent allocations, the capacity numbers won't match the resource allocator.

---

## Step 2: Design the HTML Shell

### Header

Match the resource allocator exactly: dark `#1a1a1a` bar, brand name left-aligned, tool title in muted color, date right-aligned.

```html
<div class="header">
  <div class="header-left">
    <div class="header-brand">Sidebar Creative</div>
    <div class="header-title">Capacity Planner</div>
  </div>
  <div class="header-date" id="headerDate"></div>
</div>
```

### Stat Cards

Four cards in a CSS grid, each with a label and a value element with a color class:

| Card | ID | Class |
|------|-----|-------|
| Bottlenecks (>100%) | statBottlenecks | red |
| Bench Openings (<70%) | statBench | green |
| Hypothetical Projects | statHypothetical | blue |
| Net Capacity Change | statNetChange | (dynamic) |

### Three-Panel Layout

Use flexbox: `main-content` (flex:3) containing two stacked panels, and `sidebar` (flex: 0 0 260px) with one panel.

```html
<div class="layout">
  <div class="main-content">
    <div class="panel" id="forecastPanel">...</div>
    <div class="panel" id="projectsPanel">...</div>
  </div>
  <div class="sidebar">
    <div class="panel" id="risksPanel">...</div>
  </div>
</div>
```

Each panel has a dark `.panel-header` and a `.panel-body`.

---

## Step 3: Write the Data Layer

### Constants

```javascript
const MONTHS = ['apr', 'may', 'jun'];
const MONTH_LABELS = { apr: 'April', may: 'May', jun: 'June' };
const DEPTS = ['design', 'development', 'strategy', 'content', 'account'];
const DEPT_LABELS = { design: 'Design', development: 'Development', strategy: 'Strategy', content: 'Content', account: 'Account/PM' };
const WEEK_MONTH_FACTOR = 4.33;
const HEALTH_COLORS = { green: '#28a745', yellow: '#ffc107', red: '#dc3545' };
```

`WEEK_MONTH_FACTOR = 4.33` converts weekly hours to monthly hours (52 weeks / 12 months).

### TEAM Array

Copy exactly from resource_allocator.html. Each entry:
```javascript
{ id, name, initials, role, dept, capacity, billableTarget, currentLoad }
```

### PROJECTS Array

Same 12 projects as the resource allocator, but extended with timeline fields:
- `endMonth` — null for retainers, 'apr'/'may'/'jun'/'jul+' for project-based
- `endsHalf` — true if the project ends mid-month (use 0.5 multiplier for that month)

### allocations Object

Copy exactly from resource_allocator.html. Key format:
```javascript
allocations['project_id']['person_id'] = weeklyHours;
```

---

## Step 4: Write Utility Functions

The key insight is the `getProjectMonthMultiplier` function that handles project timelines:

```javascript
function getProjectMonthMultiplier(project, month) {
  if (!project.endMonth || project.endMonth === 'jul+') return 1;
  const monthIndex = MONTHS.indexOf(month);
  const endIndex = MONTHS.indexOf(project.endMonth);
  if (endIndex === -1) return 1;
  if (monthIndex < endIndex) return 1;       // before end: full demand
  if (monthIndex === endIndex) return project.endsHalf ? 0.5 : 1;  // end month: half
  return 0;                                   // after end: no demand
}
```

This drives all the downstream capacity calculations. Risk adjustments layer on top via `getRiskAdjustment()`, which checks three toggle states: velloChurns, kovaDownsizes, summitExpands.

---

## Step 5: Add State Variables and Render Stubs

```javascript
let hypotheticalProjects = [];
let riskToggles = { velloChurns: false, kovaDownsizes: false, summitExpands: false };
let expandedDept = null;

function renderForecast() {}
function renderProjects() {}
function renderRisks() {}
function updateStats() {}

function renderAll() { renderForecast(); renderProjects(); renderRisks(); updateStats(); }
```

Stubbing the render functions allows the scaffold to load without errors while subsequent tasks implement the actual rendering.

---

## Final Output

**File:** `tools/capacity_planner.html`

The file is fully self-contained — all CSS, HTML, and JavaScript in one file. It loads without errors and renders the chrome (header, stat cards, empty panels). The data layer is fully wired and ready for render functions to consume.

---

## Key Decisions

1. **Copy data exactly** — never invent allocations or team members; the numbers must match the resource allocator
2. **Stub render functions** — keeps the scaffold loadable while tasks are split across sessions
3. **Timeline fields on PROJECTS** — adding `endMonth` and `endsHalf` directly to project objects keeps the data model clean and the multiplier function simple
4. **CSS self-contained** — no external dependencies; the file works standalone

---

## Prompts Used

> You are implementing Tasks 1-2 for the capacity planner: HTML scaffold and data layer. Create `tools/capacity_planner.html` with the same visual conventions as `tools/resource_allocator.html`. Include header, 4 stat cards, three-panel layout, and a full data layer. CRITICAL: Read `tools/resource_allocator.html` first to copy the exact TEAM array and allocations object.
