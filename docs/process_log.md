---
title: Process Log
version: 1.1
created: 2026-04-06
updated: 2026-04-07
---

# Process Log

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
