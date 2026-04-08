---
title: Process Log
version: 1.7
created: 2026-04-06
updated: 2026-04-08
---

# Process Log

---

## 2026-04-08 — Guided Tour Page (index.html)

**Prompt:**
Generate the index.html guided tour page for this project. Showcase all tools in sequence. For each tool show the name, the problem it solves, the prompt that built it, and an embedded preview. Include a narrative intro about building a complete agency operations suite with Claude Code. Commit and push when done.

**What was built:**
- `index.html` — Guided tour page showcasing all 5 tools with:
  - Hero section with project stats (5 tools, 40 team members, 8 clients, $69K at risk, $220K retainers)
  - Sticky navigation bar linking to each tool section
  - Narrative intro about building an agency operations suite with Claude Code
  - 5 tool sections, each with: numbered header, problem statement, original prompt (in dark code block), and embedded iframe preview with "Open Full Size" link
  - Browser-chrome preview frames with red/yellow/green dots
  - Footer with project summary stats

**Key decisions:**
- Used the 5 tools that actually exist (not the 7 mentioned in the prompt — 2 were never built)
- Embedded live iframes rather than screenshots for interactive previews
- Used lazy loading on iframes to avoid loading all 5 tools at once
- Prompts shown are the original user prompts, not the subagent dispatch prompts

**Files created/modified:**
- `index.html` (created)
- `docs/process_log.md` (modified)

---

## 2026-04-07 — Client Health Scorer (Tool 5)

**Prompt:**
Build a client health scorer tool at `tools/client_health.html` — a single self-contained HTML file with zero dependencies. A ranked list of 8 agency clients scored on health, sorted worst-first. Read-only scorecard with 4 stat cards, score circles, 3 dimension bars per client, and risk/highlights box.

**What was built:**
- `tools/client_health.html` — Read-only client health scorecard with:
  - Dark header matching existing tools (Sidebar Creative / Client Health Scorer / date)
  - 4 stat cards: Portfolio Health (74), Revenue at Risk ($69K/mo in red), Green/Yellow/Red distribution (5/2/1), Avg Effective Rate
  - 8 client cards sorted worst-first by composite score (44→91)
  - Each card: 4px left border by status, 48px score circle with tinted background, name/meta/rate line, badge(s), 3 dimension bars in a CSS grid, risk/highlights box
  - Dimension bar colors driven by that dimension's own score (not composite), enabling green clients with red profitability bars
  - All stats calculated dynamically from JS data array

**Key decisions:**
- Dim colors independent of composite: `dimColor(score)` applied per-dimension, not per-client status — matches spec requirement
- `riskHeader` field per client drives label text ('Risk Factors' vs 'Highlights') so red/yellow and green clients differ correctly
- Stats derived from data (not hardcoded) so future data updates auto-propagate to stat cards
- Yellow score in score circle uses `#d49e00` for legibility on white background (not raw `#ffc107`)
- Matched all visual conventions: dark header, #f5f5f5 bg, white cards, system font, same box-shadow/border-radius as existing tools

**Files created or modified:**
- `tools/client_health.html` (created)
- `docs/process_log.md` (updated)
- `tutorials/05_client_health.md` (created)

---

## 2026-04-07 — Status Report Generator (Tool 4)

**Prompt:**
You are implementing a status report generator for a creative agency. Create `tools/status_reports.html` — a single self-contained HTML file. A two-panel report viewer: left panel selects reports, right panel displays them. Include all 8 clients with full data, plus a Monday Leadership Roll-Up.

**What was built:**
- `tools/status_reports.html` — Interactive status report viewer with:
  - Two-panel layout: selector panel (300px, sticky) + viewer panel (flex: 1)
  - Roll-up entry at top of selector with 8 client entries below (health dot, name, retainer, score)
  - Roll-up view: attention accounts with highlights, on-track table, portfolio summary table
  - Client view: progress, blockers, next steps, budget health table, key metrics table with MoM change colors
  - Copy Report button that assembles plain-text report to clipboard
  - 4 stat cards: Reports Generated (9), Red Accounts, Yellow Accounts, Avg Health Score
  - All 8 clients: Evergreen, Brightwash, Vello, Petal & Thorn, Kova, Drift Coffee, Summit Pet, Moonridge

**Key decisions:**
- Matched existing tools' dark header (#1a1a1a), #f5f5f5 background, white card panels, system sans-serif
- Health dot uses 8px circle with HEALTH_COLORS map; health badge uses semi-transparent background
- Metric direction ('up'/'down'/'flat') drives change color (green/red/gray) independent of sign
- Copy function assembles clean plain-text report with section dividers for paste into email/Slack
- metricsNote (Petal & Thorn) rendered as italic disclaimer above metrics table

**Files created or modified:**
- `tools/status_reports.html` (created)
- `docs/process_log.md` (updated)
- `tutorials/04_status_reports.md` (created)

---

## 2026-04-07 — Capacity Planner (Tool 3)

**Prompt:**
Build a capacity planner for Sidebar Creative. Read the team roster, current project allocations, and upcoming project pipeline. Forecast team capacity for the next quarter — show where bottlenecks will form, which roles need hiring or contracting, and when bench time opens up. Let me add hypothetical new projects and see how they impact capacity. Output as an interactive HTML file.

**What was built:**
- `tools/capacity_planner.html` — Interactive capacity planner with:
  - Split-view layout: department capacity forecast (top), project list (bottom), risk toggles (sidebar)
  - 5 department rows (Design, Dev, Strategy, Content, Account/PM) x 3 month columns (Apr/May/Jun)
  - Color-coded capacity bars: red (>100% bottleneck), yellow (90-100% tight), gray (70-90% normal), green (<70% bench)
  - Expandable department rows showing individual team member capacity
  - Auto-generated hiring signals (bottleneck warnings, bench time alerts)
  - Project list with monthly hours, timeline indicators, and project end dates
  - 3 pipeline risk toggles: Vello Churns, Kova Downsizes, Summit Expands
  - Hypothetical project feature: add new projects with department-level hours per month
  - 4 summary stat cards: bottlenecks, bench openings, hypothetical count, net capacity change
  - All calculations update in real time on toggle/edit

**Key decisions:**
- Monthly granularity (not weekly) — matches how agencies budget and plan
- Split view — top answers "where are bottlenecks?", bottom answers "which projects cause them?"
- Risk toggles over scenario builder — independent toggles are more flexible and simpler
- Inline hypothetical projects — consistent with resource allocator interaction pattern
- Department-level forecasting (not individual) — resource allocator handles person-level

**Files created/modified:**
- `tools/capacity_planner.html` (created)
- `docs/superpowers/specs/2026-04-07-capacity-planner-design.md` (created)
- `docs/superpowers/plans/2026-04-07-capacity-planner.md` (created)
- `tutorials/03_capacity_planner.md` (created)
- `docs/process_log.md` (modified)

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
