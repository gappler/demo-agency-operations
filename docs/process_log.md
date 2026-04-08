---
title: Process Log
version: 1.3
created: 2026-04-06
updated: 2026-04-07
---

# Process Log

---

## 2026-04-07 — Capacity Planner Scaffold (Tool 3, Tasks 1–2)

**Prompt:**
You are implementing Tasks 1-2 for the capacity planner: HTML scaffold and data layer. Create `tools/capacity_planner.html` — a single self-contained HTML file with: same visual conventions as resource_allocator.html (dark header, system fonts, status colors); a header with "Sidebar Creative" / "Capacity Planner" / date; 4 stat cards (Bottlenecks, Bench Openings, Hypothetical Projects, Net Capacity Change); a three-panel layout with Capacity Forecast, Projects Driving Demand, and Pipeline Risks panels; and a full data layer with TEAM array, PROJECTS array with timeline fields, allocations object, and all utility/forecast functions.

**What was built:**
- `tools/capacity_planner.html` — HTML scaffold and data layer including:
  - Header, 4 stat cards, three-panel flexbox layout (main-content + sidebar)
  - Full CSS (panels, forecast table, projects table, risk items, toggle switches, hypothetical project form styles)
  - TEAM array (33 billable staff, copied exactly from resource_allocator.html)
  - PROJECTS array (12 projects with added endMonth/endsHalf timeline fields)
  - allocations object (all 12 project allocations, copied exactly)
  - All utility/forecast functions: getProjectMonthMultiplier, getProjectDeptMonthHours, getDeptMonthDemand, getDeptCapacity, getCapacityColor, getCapacityLabel, formatMoney, getRiskAdjustment, getPersonMonthDemand, getPersonRiskAdjustment, getProjectMonthTotal, getProjectRiskAdjustment, isProjectRiskAffected, getTimelineTag
  - State variables: hypotheticalProjects, riskToggles, expandedDept
  - Render stubs for later tasks: renderForecast, renderProjects, renderRisks, updateStats

**Key decisions:**
- TEAM and allocations copied exactly from resource_allocator.html — no invented data
- PROJECTS extended with endMonth/endsHalf timeline fields (retainers get null, project-based get specific end months)
- All CSS self-contained in file, matching visual conventions of resource_allocator.html
- Render functions left as stubs per task scoping — wired to renderAll() which is called on load

**Files created/modified:**
- `tools/capacity_planner.html` (created)
- `docs/process_log.md` (modified)

---

## 2026-04-07 — Resource Allocator (Tool 2)

**Prompt:**
Build a resource allocator for Sidebar Creative. Read the team roster and project data. Show each team member's current capacity — hours allocated vs. available — with visual indicators for overloaded (red) and underutilized (green) staff. Let me drag or reassign team members across projects and see capacity update in real time. Output as an interactive HTML file.

**What was built:**
- `tools/resource_allocator.html` — Interactive resource allocation matrix with:
  - Projects as rows (8 retainers + 4 project-based engagements), team members as columns
  - Department filter tabs (All, Design, Development, Strategy, Content, Account/PM)
  - Week/Month toggle for SOW-level vs. working-unit views
  - Click-to-edit hour cells with real-time capacity recalculation
  - Over-allocation warning with confirmation dialog and alternative suggestions
  - Sticky left column (project info) and footer row (capacity totals)
  - Color-coded utilization: red (>115%), green (<70%), gray (normal)
  - 4 summary stat cards: overloaded count, underutilized count, total hours, avg utilization
  - All 33 billable staff with allocations summing to their known current load

**Key decisions:**
- Projects-as-rows orientation (mirrors Monday meeting flow: walk through projects, not people)
- Click-to-edit over drag-and-drop (faster for real allocation work in meetings)
- Soft over-allocation constraint with confirmation + alternative suggestions
- Weekly default with monthly toggle (working unit vs. SOW review)
- Department filter tabs to manage column count
- Excluded Leadership (4) and Ops/Admin (3) as non-billable staff

**Files created/modified:**
- `tools/resource_allocator.html` (created)
- `docs/superpowers/specs/2026-04-07-resource-allocator-design.md` (created)
- `docs/superpowers/plans/2026-04-07-resource-allocator.md` (created)
- `tutorials/02_resource_allocator.md` (created)
- `docs/process_log.md` (modified)

---

## 2026-04-07 — Project Timeline Dashboard (Tool 1)

**Prompt:**
Build a project timeline dashboard for Sidebar Creative. Read the brand definition and client portfolio data. Show all active projects on an interactive Gantt-style timeline with start dates, deadlines, current progress, and RAG status. Include the 8 retainer clients and the project-based engagements. Output as an interactive HTML file.

**What was built:**
- `tools/project_timeline.html` — Complete single-file interactive dashboard with:
  - Dark branded header with agency name, page title, and current date (April 7, 2026)
  - 4 summary stat cards (active engagements, at-risk count, on-track count, next deadline)
  - Filter bar toggling between All (12), Retainers (8), Projects (4)
  - Gantt timeline with two sections (Retainer Clients, Project Engagements)
  - Horizontal bars colored by health status with progress fill
  - Diamond milestone markers, purple renewal markers, red "Today" line
  - Overflow arrows for bars extending beyond the visible year
  - Hover tooltips with key stats
  - Click-to-open detail panel with budget, health scores, milestones, team pills, and risk notes
  - All 8 retainer clients and 4 project engagements with complete data

**Key decisions:**
- Single HTML file with all CSS in `<style>` and all JS in `<script>` — zero external dependencies
- CSS Grid for Gantt layout (220px label column + 12 month columns)
- Bars and markers are overlaid on rows after render using absolute positioning relative to cell bounding rects
- Re-renders on window resize to keep bar positions accurate
- Health color mapping: retainers use green/yellow/red; projects use green/yellow/blue (early-stage)
- Utilization bar color-codes: green <=100%, yellow <=110%, red >110%
- Score color thresholds: >=75 green, >=50 yellow, <50 red
- At-risk count includes both red and yellow health statuses
- On-track count includes both green and blue (early-stage) statuses

**Files created or modified:**
- `tools/project_timeline.html` (created)
- `docs/process_log.md` (modified)
- `tutorials/01_project_timeline.md` (created)

---

## 2026-04-06 — Brand Definition + Client Reporting Data

**Prompt:**
Two-part request: (1) Create a brand definition for Sidebar Creative, a fictional 40-person Portland digital agency doing $6.5M/yr, with full team roster, 8 active clients, project portfolio, and operational pain points. (2) Extend with client reporting tools context — 6 months of historical performance data, SOW terms, client health scoring framework, and the reporting objective of replacing Friday afternoon manual report assembly.

**What was built:**
- `brand_definition.md` — Complete agency profile including overview, 40-person team roster with capacity data, 8 retainer client details, 4 project-based engagements, operational pain points narrative, client reporting context, health score summary, and operational objectives
- `data/client_sow_terms.md` — SOW terms for all 8 clients with monthly deliverables, hour allocations by discipline, key terms, and a portfolio summary showing hour utilization and effective rates
- `data/client_performance_history.md` — 6 months (Oct 2025–Mar 2026) of monthly metrics for all 8 clients: website traffic, lead generation, social media, email performance, and hours delivered vs. SOW
- `data/client_health_framework.md` — Three-dimension scoring framework (Performance 40%, Profitability 35%, Relationship 25%) with scoring criteria, current scores for all clients, portfolio health summary, and automation opportunities

**Key decisions:**
- Set retainer range at $12K–$45K summing to $220K/month ($2.64M annualized), with remaining $3.86M from project work and consulting — matches the $6.5M annual revenue target
- Made Evergreen Outdoor the recently-expanded client ($35K→$45K in Jan 2026) as the largest and most established account
- Made Vello Cycling the churn risk (🔴 Red, score 44) — driven by new VP Marketing stakeholder change, declining metrics, and missed deadlines
- Made Kova Skincare (🟡 Yellow, score 64) and Moonridge Spirits (🟡 Yellow, score 60) the underperformers for different reasons: Kova has flat metrics despite a redesign; Moonridge has profitability issues from scope overruns
- Overloaded team members (Nico 141%, Owen 135%, Sophie 131%, Celeste 127%, Bea 121%) deliberately map to the busiest accounts (Evergreen, Vello) — showing how "safe choice" staffing creates bottlenecks
- Underutilized members (Elodie 56%, Elliot 59%, Ava 65%, Casey 66%) are junior/mid-level staff not getting opportunities
- Performance data trends are internally consistent: Vello's declining metrics match its Red health status; Evergreen's strong numbers match its expansion; Moonridge's data starts in Dec 2025 matching its Dec contract start
- Health framework weighted profitability at 35% (not 33%) to reflect the agency's core pain point of invisible margins

**Files created or modified:**
- `brand_definition.md` (created)
- `data/client_sow_terms.md` (created)
- `data/client_performance_history.md` (created)
- `data/client_health_framework.md` (created)
- `docs/process_log.md` (created)
