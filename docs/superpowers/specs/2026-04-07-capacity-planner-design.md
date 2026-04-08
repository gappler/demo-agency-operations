---
title: Capacity Planner — Design Spec
version: 1.0
created: 2026-04-07
updated: 2026-04-07
status: approved
tool: tools/capacity_planner.html
parent: brand/brand_definition.md
---

# Capacity Planner — Design Spec

## Purpose

Forecast Sidebar Creative's team capacity for Q2 2026 (April–June). Show where bottlenecks will form as projects end and contracts shift, which departments need hiring or contracting, and when bench time opens up. Let users add hypothetical projects and toggle pipeline risks to model different futures.

This tool answers: "Can we take on new work next month, and if so, what kind?"

## Layout

### Split View

The page is divided into two halves with a right sidebar:

- **Top half:** Department capacity forecast — 5 department rows x 3 month columns showing demand vs. available capacity
- **Bottom half:** Project list — the engagements driving the demand numbers, with an "add hypothetical project" row
- **Right sidebar:** Pipeline risk toggles

The two halves are linked: changes in the bottom (adding projects, toggling risks) instantly update the top.

### Header and Stats

Same pattern as existing tools:
- Dark header (#1a1a1a) with "Sidebar Creative — Capacity Planner" and current date
- 4 summary stat cards:
  1. **Bottlenecks** — count of department-months over 100% (red)
  2. **Bench Openings** — count of department-months under 70% (green)
  3. **Hypothetical Projects** — count of user-added projects
  4. **Net Capacity Change** — total hours freed or added by active risk toggles

## Top Half: Department Capacity Forecast

### Rows

5 departments, one row each:
1. **Design** (12 staff, 408 billable hrs/mo target)
2. **Development** (8 staff, 272 billable hrs/mo target)
3. **Strategy** (4 staff, 120 billable hrs/mo target)
4. **Content** (3 staff, 102 billable hrs/mo target)
5. **Account/PM** (6 staff, 192 billable hrs/mo target)

Monthly target = sum of (billableTarget x 4.33) for all staff in the department.

### Columns

3 months: **April 2026**, **May 2026**, **June 2026**.

### Capacity Bars

Each cell shows a horizontal bar:
- **Bar fill:** Demand hours as a percentage of available capacity
- **Label inside bar:** `{demand} / {available}` hours
- **Label below bar:** Percentage and status word

Color coding:
- **Red (#dc3545):** >100% — Bottleneck (demand exceeds capacity)
- **Yellow (#ffc107):** 90–100% — Tight (no room for new work)
- **Gray (#555):** 70–90% — Normal (healthy utilization)
- **Green (#28a745):** <70% — Bench time (significant available capacity)

### Expandable Rows

Clicking a department row expands it to show individual team members within that department. Each person shows their monthly demand vs. billable target across the 3 months. Click again to collapse. Only one department expanded at a time.

### Hiring Signals

Below the capacity bars, an auto-generated section lists actionable signals:
- Red text: departments that are bottlenecked in any month (e.g., "Design bottleneck in April — 44 hrs over capacity")
- Green text: departments with significant bench time (e.g., "Dev bench opens in June — 136 hrs available")
- These update in real time as risks are toggled or hypothetical projects are added.

## Bottom Half: Project List

### Table Structure

- **Left column:** Project name, monthly value or budget, and timeline indicator
- **3 month columns:** April, May, June — showing total hours demanded by that project in that month
- **Rows grouped by type:** Retainers first, then Project-Based

### Monthly Hour Calculation

For **retainers:** Monthly SOW hours continue in every month the retainer is active. These are the sum of all discipline allocations from `data/client_sow_terms.md` for that client.

For **project-based engagements:** Hours are allocated across the remaining months of the project timeline. When a project ends (e.g., River District ends April, NW Natural ends April), its hours show for that month but are zero for subsequent months. When a project ends mid-month, its hours are shown at half for that final month.

Project end dates:
- **River District Rebrand:** Ends April 2026 (half hours in April, zero May/June)
- **NW Natural Rebrand:** Ends April 2026 (half hours in April, zero May/June)
- **Cascade Athletic Website:** Ends June 2026 (full April/May, half hours in June)
- **Fieldwork Provisions Launch:** Runs through July 2026 (full hours all 3 months)

### Timeline Indicators

Projects that end during the quarter show a colored label:
- "Ends Apr" in red for projects ending soon
- "Ends Jun" in green for projects ending later
- "Ongoing" in gray for retainers and projects running past June

### Hypothetical Project Row

A "**+ New Project**" row at the bottom of the project list with dashed blue border (#007bff). Clicking it adds a new editable row:
- Editable project name (text input, defaults to "New Project")
- 3 month cells. Each cell shows a total. Clicking a month cell opens a small dropdown/popover with 5 number inputs (one per department: Design, Dev, Strategy, Content, Account/PM). Enter hours per department, the cell shows the sum, and the capacity bars update.
- Hypothetical rows use a blue dashed border and light blue background (#007bff08) to distinguish from real projects.
- A small "x" button on the left removes a hypothetical project.
- Multiple hypothetical projects can be added.

When hypothetical project hours are entered, the top-half capacity bars update in real time.

## Right Sidebar: Pipeline Risk Toggles

### Toggle List

3 toggles based on known pipeline risks from the brand definition:

1. **Vello Churns** (OFF by default)
   - Effect: Removes all Vello Cycling allocations from May and June (assumes 30-day notice, April hours remain)
   - Impact: Frees ~77 hrs/mo across Design, Dev, Strategy, Content

2. **Kova Downsizes** (OFF by default)
   - Effect: Reduces Kova Skincare allocations by ~30% starting June (assumes July quarterly review triggers change, with June as transition)
   - Impact: Reduces ~44 hrs/mo

3. **Summit Expands** (OFF by default)
   - Effect: Increases Summit Pet Supply allocations by ~55% starting May (assumes retainer grows from $18K to $28K)
   - Impact: Adds ~62 hrs/mo

### Toggle Behavior

Each toggle is a styled on/off switch. When toggled ON:
- The switch slides right and turns colored (red for churns/downsizes, green for expands)
- The affected project row in the bottom half shows the adjustment (strikethrough for removed hours, highlighted for added)
- The top-half capacity bars recalculate instantly
- The stat cards update

### Hiring Signals Section

Below the toggles, an auto-generated section that reads the current forecast and lists:
- Departments where any month exceeds 100% capacity — suggest hiring or contracting
- Departments where any month drops below 60% — suggest finding new work or rebalancing
- Signals update in real time as toggles and hypothetical projects change

## Data Sources

### Team Members

From `brand/brand_definition.md` Team Roster and `tools/resource_allocator.html` data. 33 billable staff across 5 departments with known billableTarget values.

### Current Allocations

From the allocation matrix in `tools/resource_allocator.html`. Each person's current weekly allocations, converted to monthly (x 4.33), grouped by project and department.

### Project Timelines

From `brand/brand_definition.md` Active Client Portfolio:
- 8 retainers with SOW periods (all continue through Q2 2026)
- 4 project-based engagements with end dates

### Pipeline Risks

From `brand/brand_definition.md` client details:
- Vello Cycling: SOW expired, month-to-month, competitive pitches requested
- Kova Skincare: ROI questioned, possible downsize at July quarterly review
- Summit Pet Supply: Verbal interest in expanding to $25-30K

## Visual Design

Matches existing tool conventions:
- **Header:** Dark bar (#1a1a1a) with white text
- **Background:** #f5f5f5
- **Cards/sections:** White (#fff) with subtle shadow
- **Typography:** System sans-serif, 13px base
- **Status colors:** Red (#dc3545), Yellow (#ffc107), Green (#28a745), Gray (#555)
- **Hypothetical elements:** Blue (#007bff) dashed borders, light blue tint

## Technical Implementation

- **Single self-contained HTML file** — all CSS and vanilla JavaScript inlined, zero dependencies
- **Data embedded as JavaScript objects** — reuses the same team/project/allocation structure as the resource allocator
- **State is in-memory only** — hypothetical projects and toggle states don't persist across reloads

## Out of Scope

- Persistent storage
- Multi-user collaboration
- Integration with external tools
- Quarters beyond Q2 2026
- Individual-level capacity optimization (the resource allocator handles that)
- Budget/revenue forecasting (this is about hours and headcount)
