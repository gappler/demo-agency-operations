---
title: "Tutorial 07: Client Report Generator"
tool: client_reports.html
version: 1.0
created: 2026-04-07
---

# Tutorial 07: Building the Client Report Generator

## Overview

This tutorial walks through building an automated client report generator that produces polished, client-facing monthly performance reports with markdown export.

## Step 1: Review Existing Data

Before building, read `tools/performance_dashboard.html` to get the exact performance data arrays for all 8 clients. This ensures data consistency across tools.

**Prompt used:**
> Build an automated client report generator at `tools/client_reports.html` — a single self-contained HTML file with zero dependencies. A tool with two dropdowns (client + month) and a Generate button. Clicking Generate renders a polished, client-facing monthly performance report below. An "Export Markdown" button copies the report as clean markdown.

## Step 2: Structure the Data

The tool needs three categories of data per client:

1. **Performance metrics** (from performance_dashboard.html) — Traffic, Leads, Social, Email, Hours across 6 months
2. **SOW terms** — Deliverables and hours by discipline (Account Management, Strategy, Design, Development, Content, Paid Media)
3. **Pre-authored content** — March executive summaries and strategic recommendations

## Step 3: Build the Controls

The controls area uses a white card with:
- Client dropdown with 8 clients showing retainer values
- Month dropdown (Oct 2025 through Mar 2026)
- Generate Report button (dark background)
- Export Markdown button (green, hidden until first generation)

Default selection is Evergreen Outdoor Co. + Mar 2026, and the report generates automatically on page load.

## Step 4: Report Generation Logic

The report has 6 sections:

1. **Report Header** — Branding, client name, month, health badge (colored pill), account director, date
2. **Executive Summary** — Pre-authored for March; template-generated for other months based on metric trends
3. **Performance Metrics** — 4 subsections with tables showing current month, prior month, and MoM change
4. **Deliverables & SOW** — Table of disciplines with deliverables and allocated hours
5. **Hours & Budget** — SOW hours, actual hours, utilization, budget, cost, effective rate
6. **Strategic Recommendations** — Pre-authored for March; data-driven for other months

### Key Decision: Template-Based Summaries

For non-March months, the tool generates summaries by:
- Counting metrics that improved vs declined to determine tone (strong/steady/challenging)
- Identifying the best and worst performing metrics for narrative detail

### Key Decision: Inverted Color Logic

For metrics where lower is better (bounce rate, cost per lead), the MoM change colors are inverted — a decrease shows green, an increase shows red.

## Step 5: Markdown Export

The Export Markdown button uses the Clipboard API to copy the full report as clean markdown:
- `#` for report title, `##` for sections
- Pipe tables for all data tables
- Numbered list for recommendations
- Health score emoji indicators

Shows "Copied!" feedback for 2 seconds.

## Step 6: Visual Styling

Match existing tools:
- Dark #1a1a1a header
- System sans-serif font stack
- #f5f5f5 background
- White cards with `box-shadow: 0 1px 3px rgba(0,0,0,.08)`, 6px border-radius
- Report area: max-width 900px, centered, 40px padding, 15px body text, line-height 1.7
- Tables: 13px, alternating row backgrounds
- Status colors: green #28a745, yellow #ffc107, red #dc3545

## Final Output

`tools/client_reports.html` — A self-contained HTML file that generates professional monthly performance reports for any of 8 agency clients across 6 months, with one-click markdown export.
