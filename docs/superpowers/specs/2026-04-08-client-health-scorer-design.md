---
title: Client Health Scorer — Design Spec
version: 1.0
created: 2026-04-08
updated: 2026-04-08
status: approved
tool: tools/client_health.html
parent: brand/brand_definition.md
---

# Client Health Scorer — Design Spec

## Purpose

Score each of Sidebar Creative's 8 retainer clients on a composite health scale (0-100) across three dimensions: Performance, Profitability, and Relationship. Rank clients worst-first to surface risk. Flag the at-risk client (Vello — churn risk) and the recently expanded one (Evergreen — retainer grew from $35K to $45K). Show the scoring breakdown so partners can see exactly why a client is red, yellow, or green.

## Layout

### Ranked List (Worst-First)

All 8 clients displayed as wide cards in a single column, sorted by composite health score ascending (lowest/worst at top). Each card shows the full scoring breakdown and key risk factors or highlights.

### Header and Stats

Same pattern as existing tools:
- Dark header (#1a1a1a) with "Sidebar Creative — Client Health Scorer" and current date
- 4 summary stat cards:
  1. **Portfolio Health** — average composite score across all 8 clients (74)
  2. **Revenue at Risk** — sum of monthly retainers for yellow + red clients ($69K/mo)
  3. **Green / Yellow / Red** — status counts (5 / 2 / 1)
  4. **Avg Effective Rate** — blended effective hourly rate across portfolio

## Client Cards

Each client card contains:

### Card Header
- **Left border:** 4px solid, color-coded by status (red/yellow/green)
- **Score circle:** 48px circle with tinted background, large composite score number in status color
- **Client name:** Bold, 15px
- **Meta line:** Retainer amount, account director name
- **Status badges** (right-aligned): One or more small badges:
  - Red clients: "AT RISK" badge. Vello also gets "CHURN RISK" badge.
  - Yellow clients: "WATCH" badge
  - Green clients: "HEALTHY" badge
  - Evergreen: additional "EXPANDED ↑" badge (blue, #007bff)
  - Summit: additional "EXPANSION OPP" badge (blue, #007bff)

### Dimension Bars

3 horizontal bars in a grid (1fr 1fr 1fr):
- **Performance** (40% weight) — score and filled bar
- **Profitability** (35% weight) — score and filled bar
- **Relationship** (25% weight) — score and filled bar

Each bar is 6px tall with rounded corners. The bar fill color is based on that dimension's individual score, not the composite:
- Score >= 80: green (#28a745)
- Score 60-79: yellow (#ffc107)
- Score < 60: red (#dc3545)

The score number to the right of the label uses the same color as the bar.

### Key Risk Factors / Highlights

A tinted box below the dimension bars:
- **Red/yellow clients:** Background tinted to match status. Header "KEY RISK FACTORS" in status color. 2-3 bullet points explaining the primary risks from the health framework dimension notes.
- **Green clients:** Light green background. Header "HIGHLIGHTS". 2-3 bullet points on strengths and positive signals.

## Scoring Data

All scores come directly from `data/client_health_framework.md` Current Client Health Scores section:

| Client | Composite | Performance | Profitability | Relationship | Status |
|--------|-----------|-------------|---------------|--------------|--------|
| Vello Cycling | 44 | 45 | 52 | 35 | Red |
| Moonridge Spirits | 60 | 65 | 48 | 70 | Yellow |
| Kova Skincare | 64 | 55 | 70 | 72 | Yellow |
| Brightwash Home | 78 | 80 | 68 | 88 | Green |
| Drift Coffee Roasters | 83 | 78 | 88 | 85 | Green |
| Summit Pet Supply | 84 | 82 | 80 | 90 | Green |
| Petal & Thorn | 86 | 85 | 90 | 82 | Green |
| Evergreen Outdoor Co. | 91 | 92 | 85 | 95 | Green |

### Risk Factors / Highlights per Client

**Vello Cycling (44, Red):**
- Client requested competitive agency pitches — 60-day recovery window
- All metrics declining 6 consecutive months (traffic -24%, MQLs -38%)
- Hours 15-23% over SOW due to rework; effective rate $149/hr

**Moonridge Spirits (60, Yellow):**
- Brand identity phase month 5 of 3 — revision cycles 2x contracted limit
- Averaging 114% SOW hours; effective rate $136/hr (target $155/hr)
- Needs retainer increase to $15-18K or stricter scope management

**Kova Skincare (64, Yellow):**
- Web redesign hasn't moved conversion metrics; client questioning ROI
- Performance milestones not met (15% traffic growth, 10% conversion targets)
- Possible retainer downsize from $25K to $18K at July quarterly review

**Brightwash Home (78, Green):**
- Meeting most KPIs; subscription launch was a win; steady metrics
- Profitability weak spot — persistent 5-7% hour overruns from untracked requests
- Strong working relationship; client responsive and collaborative

**Drift Coffee Roasters (83, Green):**
- Subscription platform performing well; modest consistent growth
- Most efficient account — hours consistently under SOW, effective rate $169/hr
- Long-term, low-drama relationship with mutual trust

**Summit Pet Supply (84, Green):**
- Strongest growth trajectory in portfolio; metrics improving MoM across the board
- Client has asked about scope expansion twice — ready for $25-30K retainer
- Enthusiastic, responsive client; highest relationship score after Evergreen

**Petal & Thorn (86, Green):**
- Valentine's 2026 was client's best-performing campaign ever ($148K email revenue)
- Most profitable account — hours consistently under SOW, effective rate $173/hr
- Seasonal surge provision well-managed; client trusts the team

**Evergreen Outdoor Co. (91, Green):**
- Expanded retainer from $35K to $45K in Jan 2026 after strong holiday campaign
- CMO is an advocate — co-presents with Maya Chen at industry events
- Highest effective rate in portfolio at $179/hr

## Supporting Data

Each client card also shows supplementary data from `data/client_sow_terms.md` and `data/client_performance_history.md`:
- **Effective hourly rate:** Monthly retainer ÷ actual hours delivered (March 2026)
- **SOW utilization:** Actual hours vs. SOW hours as a percentage
- **Monthly retainer value**
- **Account director** from `brand/brand_definition.md` primary team assignments

## Visual Design

Matches existing tool conventions:
- **Header:** Dark bar (#1a1a1a) with white text
- **Background:** #f5f5f5
- **Cards:** White (#fff) with subtle shadow, left border color-coded
- **Typography:** System sans-serif, 13px base
- **Status colors:** Red (#dc3545), Yellow (#ffc107), Green (#28a745)
- **Special badges:** Blue (#007bff) for expansion-related callouts
- **Dimension bars:** 6px tall, rounded, color per individual dimension score
- **Risk factor boxes:** Tinted background matching status color at ~8% opacity

## Technical Implementation

- **Single self-contained HTML file** — all CSS and vanilla JavaScript inlined, zero dependencies
- **Data embedded as JavaScript objects** — client scores, dimension breakdowns, risk factors, supporting metrics
- **Sorting:** Clients sorted by composite score ascending (worst-first) on render
- **No interactivity beyond scroll** — this is a read-only scorecard, not an editor

## Out of Scope

- Editing or recalculating scores (scores are fixed from the framework data)
- Historical score trends over time
- Drill-down into individual metric data (the status reports tool handles that)
- Score simulation or what-if modeling
