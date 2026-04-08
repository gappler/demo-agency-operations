---
title: "Tutorial: Building the Capacity Planner"
version: 1.0
created: 2026-04-07
tool: tools/capacity_planner.html
---

# Tutorial: Building the Capacity Planner

Step-by-step guide to replicating the interactive capacity planner for Sidebar Creative.

---

## Step 1: Brainstorm the Design

**Prompt:**
> Build a capacity planner for Sidebar Creative. Read the team roster, current project allocations, and upcoming project pipeline. Forecast team capacity for the next quarter — show where bottlenecks will form, which roles need hiring or contracting, and when bench time opens up. Let me add hypothetical new projects and see how they impact capacity. Output as an interactive HTML file.

### Key Design Decisions

1. **Monthly granularity over weekly**
   - Agencies budget at the month level. SOW terms are monthly. 3 columns (Apr/May/Jun) keeps the view tight. Weekly would mean 13 columns — too noisy for strategic planning.

2. **Split-view layout**
   - Top half answers "where are the bottlenecks by department?" — the strategic question
   - Bottom half answers "which projects are driving that demand?" — the tactical question
   - Right sidebar has pipeline risk toggles — the scenario modeling layer
   - Changes in the bottom instantly update the top.

3. **Risk toggles over scenario builder**
   - Each known risk (Vello churning, Kova downsizing, Summit expanding) gets an independent ON/OFF toggle. You can mix and match ("what if Vello churns AND Summit expands?").
   - Simpler than named scenarios, more flexible, and obvious in the UI.

4. **Inline hypothetical projects**
   - Click "+ New Project" at the bottom of the project list. Enter hours per department per month. The project appears as a blue-bordered row and the capacity bars update instantly.
   - Matches the click-to-edit pattern from the resource allocator.

5. **Department-level forecasting**
   - The capacity planner answers "do I need to hire a designer?" not "should I move Ava to this project?" — that's the resource allocator's job.
   - Expandable rows let you drill into individuals when needed.

## Step 2: Understand the Data

### Project Timelines Drive the Forecast

The key insight: project-based engagements have end dates, and when they end, the capacity they consumed becomes available.

- **River District Rebrand:** Ends April (half hours in Apr, zero May/Jun)
- **NW Natural Rebrand:** Ends April (half hours in Apr, zero May/Jun)
- **Cascade Athletic Website:** Ends June (full Apr/May, half Jun)
- **Fieldwork Provisions:** Through July (full hours all 3 months)

Retainers continue at steady monthly rates unless a risk toggle changes them.

### Risk Adjustments

Each toggle modifies the allocation data for specific months:
- **Vello Churns:** Removes all Vello allocations from May and June (30-day notice, April stays)
- **Kova Downsizes:** Reduces Kova by 30% in June only (quarterly review triggers change)
- **Summit Expands:** Increases Summit by 55% from May onward ($18K to $28K)

## Step 3: Build the Scaffold

Create `tools/capacity_planner.html` with the three-panel layout:
- Header, stat cards (same pattern as existing tools)
- Main content area with forecast panel + projects panel
- Sidebar with risks panel
- Embed all data: TEAM, PROJECTS (with endMonth/endsHalf), allocations

## Step 4: Capacity Calculation Engine

The core function chain:
1. `getProjectMonthMultiplier(project, month)` — returns 1, 0.5, or 0 based on end date
2. `getProjectDeptMonthHours(projectId, dept, month)` — sums allocations for a dept on a project, multiplied by the month multiplier
3. `getDeptMonthDemand(dept, month)` — sums across all projects + risk adjustments + hypothetical projects
4. `getDeptCapacity(dept)` — sum of billableTarget x 4.33 for all staff in dept

## Step 5: Render the Forecast

Department rows with capacity bars (demand/capacity percentage). Color thresholds:
- Red >100%: Bottleneck
- Yellow 90-100%: Tight
- Gray 70-90%: Normal
- Green <70%: Bench time

Auto-generated hiring signals flag departments above 100% or below 60%.

## Step 6: Render Project List and Risk Toggles

Project table shows monthly hours with timeline indicators. Risk toggles recalculate the entire forecast on change. Affected project rows show visual indicators (strikethrough for removed, highlight for added).

## Step 7: Hypothetical Projects

Form-based entry: name + hours per department per month. Added projects appear as blue-bordered cards and feed into the demand calculation. Remove with "x".

## Final Output

`tools/capacity_planner.html` — a single self-contained HTML file with zero dependencies. Open in any modern browser.
