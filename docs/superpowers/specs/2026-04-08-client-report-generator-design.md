---
title: Automated Client Report Generator — Design Spec
version: 1.0
created: 2026-04-08
updated: 2026-04-08
status: approved
tool: tools/client_reports.html
parent: brand/brand_definition.md
---

# Automated Client Report Generator — Design Spec

## Purpose

Generate polished, client-facing monthly performance reports for any of Sidebar Creative's 8 retainer clients. Replace the manual Friday Google Slides assembly process (2-3 hours per client) with a tool that selects a client and month, generates the full report, and exports it as formatted markdown.

This is the *client-facing* deliverable — polished enough to present in a QBR or email to a CMO. Distinct from Tool 4 (Status Reports), which generates *internal* weekly summaries.

## Interface

### Top Controls

Two dropdowns side by side + a Generate button:
- **Select Client** — dropdown with all 8 retainer clients, showing name and retainer value
- **Select Month** — dropdown with Oct 2025 through Mar 2026 (6 months)
- **Generate Report** button — renders the report below
- **Export Markdown** button — appears after report is generated, copies clean markdown to clipboard

### Report Area

Below the controls, the generated report renders as a formatted document with generous whitespace and readable typography. Styled to look like a professional report, not a dashboard.

### Header and Stats

Same pattern as existing tools:
- Dark header (#1a1a1a) with "Sidebar Creative — Client Report Generator" and current date
- No stat cards — the controls replace them. The report itself provides all the data.

## Report Sections

### 1. Report Header

Branded header with:
- Sidebar Creative logo area (text-based)
- Client name (large, bold)
- Report period: "[Month] 2026 — Monthly Performance Report"
- Health score badge (colored)
- Account Director name
- Generated date

### 2. Executive Summary

2-3 sentences providing the overall picture. For March (the primary demo month), these are pre-authored with rich context per client. For older months, generated from a template:

**Template:** "[Client] [had a strong/steady/challenging] [month]. [Primary highlight from metrics — best performing category]. [Primary concern or note — weakest category or budget issue]."

**Pre-authored March examples:**
- **Evergreen:** "Evergreen Outdoor had a strong March, with website traffic rebounding 9.1% and the email program generating $168K in attributed revenue. The spring campaign is ready to launch pending client approval on the Q2 brief. Hours remained well-managed at 98% of SOW."
- **Vello:** "Vello Cycling continues to face headwinds in March, with all key metrics declining for the sixth consecutive month. Website sessions fell to 108,200 (-3.7%) while cost per lead rose to $52.00. The account recovery plan is in progress — Derek Okafor has joined all client calls and a restructured SOW proposal is due April 10."

### 3. Performance Metrics

Four subsections, each with a data table showing the selected month's values plus MoM change:

**Website Traffic:**
| Metric | [Selected Month] | Prior Month | Change |
|--------|-----------------|-------------|--------|
| Sessions | value | value | +/-% |
| Unique Visitors | value | value | +/-% |
| Bounce Rate | value | value | +/-pp |
| Avg Session Duration | value | value | +/- |
| Pages per Session | value | value | +/- |

**Lead Generation:**
| Metric | [Selected Month] | Prior Month | Change |
| MQLs, SQLs, Conversion Rate, Subscribers Added, Cost per Lead |

**Social Media:**
| Metric | [Selected Month] | Prior Month | Change |
Platform-specific metrics (varies by client — use whatever platforms are in their data)

**Email Performance:**
| Metric | [Selected Month] | Prior Month | Change |
| List Size, Sends, Open Rate, Click Rate, Revenue Attributed |

MoM change column: green for improvement, red for decline. For metrics where lower is better (bounce rate, cost per lead), invert the color logic.

Note: October has no prior month for MoM — show "—" in the change column.

### 4. Deliverables & SOW Alignment

A table showing what was delivered mapped to the SOW terms from `data/client_sow_terms.md`:

| Discipline | SOW Deliverables | Monthly Hours Allocated |
|------------|-----------------|------------------------|
| Account Management | [from SOW] | [hours] |
| Strategy | [from SOW] | [hours] |
| Design | [from SOW] | [hours] |
| Development | [from SOW] | [hours] |
| Content | [from SOW] | [hours] |
| Paid Media | [from SOW] | [hours] |

This is the same data regardless of month — it's the standing SOW terms.

### 5. Hours & Budget

| Metric | SOW | Actual | Variance | Status |
|--------|-----|--------|----------|--------|
| Monthly Hours | [SOW hrs] | [actual from perf data] | [+/- hrs] | [% utilization] |
| Monthly Budget | [retainer $] | [actual cost] | [+/- $] | [On/Over/Under] |
| Effective Rate | [target $/hr] | [actual $/hr] | [+/- $/hr] | [status] |

Color coding: green for on/under budget, yellow for 5-10% over, red for >10% over.

### 6. Strategic Recommendations

3-4 numbered, actionable recommendations.

**For March (pre-authored per client):**
Each client gets specific, context-aware recommendations. Examples:

**Evergreen:**
1. Launch the spring paid media campaign immediately upon Q2 brief approval — the $85K media budget is ready and seasonal timing is critical.
2. Prioritize summer collection landing page design to avoid a content bottleneck once product photography arrives April 15.
3. Use the upcoming QBR (April 18) to present the H1 performance story and discuss H2 growth targets.

**Vello:**
1. Present the relationship recovery plan to the VP Marketing on April 10 with concrete "quick win" deliverables for the next 30 days.
2. Propose restructuring the SOW to shift budget from underperforming content toward performance marketing — the current content strategy isn't resonating with the new leadership.
3. Implement weekly performance check-ins (vs. current bi-weekly) to demonstrate responsiveness and accountability during the recovery window.
4. Prepare a competitive differentiation brief highlighting Sidebar's brand expertise in the cycling/outdoor category.

**For older months (template-based):**
Generated from data patterns:
- If a metric declined: "Address the [X]% decline in [metric] by [action related to that category]."
- If hours are over SOW: "Review scope management — hours exceeded SOW by [X]% this month. Consider implementing change order process for out-of-scope requests."
- If a metric improved significantly: "Build on the [X]% improvement in [metric] by expanding [related strategy]."
- Always include one forward-looking recommendation based on the next month's known events.

## Export Markdown

The "Export Markdown" button:
1. Converts the generated report to clean markdown
2. Copies to clipboard using `navigator.clipboard.writeText()`
3. Button briefly shows "Copied!" for 2 seconds

Markdown format:
- `#` for report title, `##` for sections, `###` for subsections
- `| ` pipe tables for all data tables
- Numbered list for recommendations
- Health badge as emoji (🟢/🟡/🔴)

## Data Sources

### Performance Metrics
From `data/client_performance_history.md` — 6 months of data for all 8 clients across traffic, leads, social, email, and hours categories.

### SOW Terms
From `data/client_sow_terms.md` — discipline-level deliverables and hour allocations per client.

### Health Scores
From `data/client_health_framework.md` — composite scores and dimension breakdowns.

### Client Context
From `brand/brand_definition.md` — risk notes, team assignments, relationship details used in executive summaries and recommendations.

## Visual Design

**Report styling** — This should look more like a document than a dashboard:
- White background for the report area, generous padding (40px)
- 15px body text, 1.7 line-height for readability
- Section headers with subtle bottom borders
- Tables with clean styling, alternating row backgrounds
- Health badge as a colored pill shape
- Sidebar Creative branding in the report header

**Controls area** — matches existing tool conventions (dark header, stat card styling for the dropdowns area).

## Technical Implementation

- **Single self-contained HTML file** — all CSS and vanilla JavaScript inlined
- **Data embedded as JavaScript objects** — all 6 months of metrics for all 8 clients, SOW terms, health scores, pre-authored content for March
- **Report generated on demand** — selecting client + month + clicking Generate triggers the render
- **Markdown generation** — separate function that traverses the report data and builds markdown string

## Out of Scope

- PDF export (markdown copy covers the export need)
- Custom date ranges beyond the 6 available months
- Editing report content after generation
- Chart/graph visualizations (tables with MoM indicators are sufficient for a report)
- Multi-client comparison reports
