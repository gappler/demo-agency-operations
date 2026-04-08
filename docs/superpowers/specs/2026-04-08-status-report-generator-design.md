---
title: Status Report Generator — Design Spec
version: 1.0
created: 2026-04-08
updated: 2026-04-08
status: approved
tool: tools/status_reports.html
parent: brand/brand_definition.md
---

# Status Report Generator — Design Spec

## Purpose

Replace Sidebar Creative's manual Friday reporting process (2-3 hours per client pulling data from GA, social platforms, email tools, and CRM) with an automated report generator. Produces a structured weekly status report for each of the 8 retainer clients and an issues-first internal roll-up for the Monday leadership meeting. Reports are generated, previewed, and copied as markdown — all from a single HTML interface.

This tool addresses the "Friday Report Problem" described in the brand definition: 16-24 hours of senior account time per month spent formatting slides instead of strategic thinking.

## Interface Layout

### Two-Panel Design

- **Left panel (selector):** Lists the Monday Roll-Up at the top, then all 8 client reports below. Each client entry shows health dot, name, retainer value, and health score. Click to load a report in the viewer.
- **Right panel (viewer):** Renders the selected report as formatted HTML. Header shows report title, date range, and a "Copy Markdown" button.

### Header and Stats

Same pattern as existing tools:
- Dark header (#1a1a1a) with "Sidebar Creative — Status Reports" and current date
- 4 summary stat cards:
  1. **Reports Generated** — always 9 (8 clients + 1 roll-up)
  2. **Red Accounts** — count of clients with health score < 60
  3. **Yellow Accounts** — count of clients with health score 60-79
  4. **Avg Health Score** — mean health score across all 8 clients

## Client Report Template

### Report Header

```
# [Client Name] — Weekly Status Report
Week of March 31 – April 4, 2026
Account Director: [Name] · Health Score: [Score] [Status Emoji]
```

### Section 1: Progress

3-5 bullet points covering what was delivered or accomplished. Generated from:
- SOW deliverables for the client (from `data/client_sow_terms.md`)
- Performance trends from the most recent month (from `data/client_performance_history.md`)
- Milestone completions implied by project status (from `brand/brand_definition.md`)

Each bullet is specific and quantified where possible: "March email sends (9 total) achieved 29.6% open rate, 4.5% click rate" rather than "Email performance was good."

### Section 2: Blockers

1-3 bullet points on what's stalled or at risk. Generated from:
- Client health risk notes (from `brand/brand_definition.md` client details)
- Budget overruns (SOW hours vs. actual from `data/client_performance_history.md`)
- Declining metrics (negative MoM trends)
- Client relationship issues noted in brand definition

For green/healthy clients with no significant blockers, show: "No active blockers this week."

### Section 3: Next Steps

2-4 bullet points on upcoming work. Generated from:
- SOW deliverable cadence (recurring monthly items)
- Seasonal or calendar-driven work (from SOW key terms)
- Follow-ups implied by blockers
- Upcoming milestones or renewals

### Section 4: Budget Health

A table with two rows:

| Metric | SOW | Actual | Status |
|--------|-----|--------|--------|
| Monthly Hours | [SOW hrs] | [Actual hrs from latest month] | [%] + check/warning |
| Monthly Budget | [Retainer $] | [Actual cost] | On budget / Over / Under |

Status uses green check for within 5% of SOW, yellow warning for 5-15% over, red alert for >15% over.

### Section 5: Key Metrics (March 2026)

A table showing the latest month's headline numbers with MoM change:

| Metric | Value | MoM Change |
|--------|-------|------------|
| Sessions | [number] | [+/-% in green/red] |
| MQLs or Leads | [number] | [+/-%] |
| Email Open Rate | [%] | [+/- percentage points] |
| Top Social Metric | [number] | [+/-%] |

Metrics are selected per client based on what's most relevant from their performance history. Each client gets 4-6 key metrics. MoM change is calculated by comparing March to February values.

## Internal Roll-Up Report

### Report Header

```
# Monday Leadership Roll-Up
Week of March 31 – April 4, 2026
Prepared for: Maya Chen, Raj Patel, Lena Voss, Derek Okafor
```

### Section 1: Accounts Requiring Attention

Red and yellow clients only. For each:
- Client name with health score and status badge
- 2-3 bullet highlights: what's wrong, the business risk, and the recommended next action
- Pulled from the individual client report's blockers and health data

Clients ordered by severity (lowest health score first).

Known issues to surface:
- **Vello Cycling (44, Red):** Churn risk, new VP Marketing misalignment, missed deliverables, competitive pitches
- **Moonridge Spirits (60, Yellow):** Revision overruns, marginally profitable, exceeding SOW hours
- **Kova Skincare (64, Yellow):** ROI questioned, conversion metrics flat post-redesign, possible retainer downsize

### Section 2: Accounts On Track

Green clients in a compact table:

| Client | Health | Retainer | Status Note |
|--------|--------|----------|-------------|
| Evergreen Outdoor | 91 🟢 | $45K | Expanded retainer performing well |
| Brightwash Home | 78 🟢 | $38K | Steady metrics, subscription growing |
| ... | ... | ... | ... |

One-line status note per client, pulled from the most significant progress item.

### Section 3: Portfolio Summary

Aggregate numbers:

| Metric | Value |
|--------|-------|
| Total Monthly Retainer Revenue | $220,000 |
| Average Health Score | [calculated] |
| Blended Utilization | [calculated from SOW vs actual hours] |
| Green / Yellow / Red | [counts] |
| Accounts Over SOW Hours | [count + names] |

## Report Content Generation

All report content is generated deterministically from embedded data — no AI/LLM calls at runtime. The JavaScript generates each section by reading the structured data and applying templates:

- **Progress bullets:** Constructed from SOW deliverable descriptions + latest performance data. Template: "[Deliverable type] [specific metric or count] — [quantified result from performance data]"
- **Blocker bullets:** Derived from health risk notes and negative performance trends. If health > 80 and no negative trends, output "No active blockers this week."
- **Next steps:** Derived from SOW recurring deliverables and upcoming dates/renewals.
- **Budget table:** Direct lookup from performance history's "Hours Delivered vs. SOW" section.
- **Metrics table:** Direct lookup from performance history with MoM calculation.

Each client has a data object containing pre-written bullet content tailored to their specific situation. This ensures the reports read naturally and reference real project context rather than generating generic filler.

## Copy Markdown Feature

The "Copy Markdown" button in the report viewer header:
1. Converts the currently displayed report to clean markdown format
2. Copies to the system clipboard using `navigator.clipboard.writeText()`
3. Button text briefly changes to "Copied!" for 2 seconds, then reverts

The markdown output should be clean and paste-ready for Slack, Google Docs, or Notion:
- `#` headings, `- ` bullet lists, `| ` pipe tables
- No HTML tags in the markdown output

## Data Sources

### Client Details and Risk Notes
From `brand/brand_definition.md`:
- Client names, retainer values, health status, primary team assignments
- Risk notes for at-risk clients (Vello, Kova, Moonridge)
- Project-based engagement status

### SOW Terms
From `data/client_sow_terms.md`:
- Monthly hours allocated by discipline
- Key deliverables per discipline
- Key terms, overage policies, termination notice periods

### Performance History
From `data/client_performance_history.md`:
- 6 months of metrics: website traffic, leads, social, email, hours delivered
- Latest month (March 2026) used for current report
- February 2026 used for MoM comparison

### Health Scores
From `data/client_health_framework.md`:
- Scoring methodology (Performance 40%, Profitability 35%, Relationship 25%)
- Score thresholds (Green 80+, Yellow 60-79, Red <60)
- Current scores for all 8 clients

## Visual Design

Matches existing tool conventions:
- **Header:** Dark bar (#1a1a1a) with white text
- **Background:** #f5f5f5
- **Panels:** White (#fff) with subtle shadow
- **Typography:** System sans-serif, 13px base for report content, generous line-height (1.6) for readability
- **Status colors:** Red (#dc3545), Yellow (#ffc107), Green (#28a745)
- **Selected report:** Left panel highlight with blue left border (#007bff)
- **Section headers:** Bold, 14px, with bottom border separator

## Technical Implementation

- **Single self-contained HTML file** — all CSS and vanilla JavaScript inlined, zero dependencies
- **Data embedded as JavaScript objects** — client data, SOW terms, performance metrics, pre-written report content
- **Report content pre-authored** — each client's bullets are written once in the data layer, not dynamically generated from raw numbers. This ensures reports read naturally with proper context.
- **Clipboard API** for copy functionality
- **State is in-memory only** — no persistence across reloads

## Out of Scope

- AI/LLM-powered report generation at runtime
- PDF export
- Email/Slack integration for sending reports
- Editable report content (these are generated, not authored)
- Project-based engagement reports (only retainer clients)
- Historical report archives
