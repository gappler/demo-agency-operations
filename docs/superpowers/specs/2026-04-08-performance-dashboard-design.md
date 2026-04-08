---
title: Multi-Client Performance Dashboard — Design Spec
version: 1.0
created: 2026-04-08
updated: 2026-04-08
status: approved
tool: tools/performance_dashboard.html
parent: brand/brand_definition.md
---

# Multi-Client Performance Dashboard — Design Spec

## Purpose

Show all 8 retainer clients' performance trends at a glance with 6 months of historical data. Sparkline visualizations make trends immediately visible — which clients are growing, flat, or declining. Click into any client for the full data tables across all 5 metric categories.

## Layout

### Overview (Grid View)

**2x4 card grid** showing all 8 clients simultaneously.

Each card contains:
- **Left border:** 4px solid, color-coded by health status (green/yellow/red)
- **Header row:** Client name (bold 14px), retainer amount, health score with emoji, trend indicator
- **Sparkline grid:** 2x2 grid of 4 key metrics, each showing: metric label (11px gray), SVG sparkline (60x20), latest value

Trend indicators per client:
- **↑↑ Strong Growth** (green) — most metrics improving significantly (>5% MoM)
- **↑ Trending Up** (green) — most metrics improving modestly
- **→ Steady** (gray) — metrics flat or mixed
- **→ Flat** (yellow) — metrics stagnant despite effort
- **↓ Declining** (red) — most metrics declining

Cards are ordered by health score descending (healthiest first) — this is a performance dashboard, not a risk tool.

### Header and Stats

- Dark header (#1a1a1a) with "Sidebar Creative — Performance Dashboard" and current date
- 4 summary stat cards:
  1. **Total Clients** — 8
  2. **Avg Monthly Sessions** — average of latest month sessions across portfolio
  3. **Portfolio MQLs** — sum of March MQLs across all clients
  4. **Avg SOW Utilization** — blended hours vs. SOW percentage

### Detail View (Full-Page Takeover)

Clicking a client card replaces the grid with a detailed single-client view.

**Detail header:**
- Client name, retainer, health badge
- "← Back to Overview" button (top-left)
- Trend indicator

**5 data sections,** each containing:
- Section title (e.g., "Website Traffic")
- Larger sparkline (200x40) for the primary metric in that category
- Full 6-month data table with all metrics in that category
- MoM change column (Feb → Mar) with green/red coloring

#### Section 1: Website Traffic
Table columns: Month, Sessions, Unique Visitors, Bounce Rate, Avg Session Duration, Pages/Session

#### Section 2: Lead Generation
Table columns: Month, MQLs, SQLs, Conversion Rate, Email Subscribers Added, Cost per Lead

#### Section 3: Social Media
Table columns vary by client (each client tracks different platforms). Use the exact platforms from `data/client_performance_history.md`.

#### Section 4: Email Performance
Table columns: Month, List Size, Sends, Open Rate, Click Rate, Revenue Attributed

#### Section 5: Hours Delivered vs. SOW
Table columns: Month, SOW Hours, Hours Delivered, Utilization, Budget, Actual Cost

### Navigation

- Click any card in the grid → detail view for that client
- "← Back to Overview" button in detail view → returns to grid
- State tracked in JavaScript (no URL changes)

## Sparkline Implementation

Pure SVG `<polyline>` elements — no charting library.

**Generation algorithm:**
1. Take 6 data points (Oct–Mar)
2. Find min and max values in the series
3. Map each point to X,Y coordinates:
   - X: evenly spaced across the viewBox width (with padding)
   - Y: normalized to viewBox height, inverted (higher value = lower Y)
4. Join as `<polyline points="x1,y1 x2,y2 ...">` with no fill, stroke color based on trend

**Sparkline colors:**
- Green (#28a745): upward trend (last value > first value by >5%)
- Red (#dc3545): downward trend (last value < first value by >5%)
- Gray (#555): flat or mixed

**Hours vs. SOW sparklines** include a dashed horizontal reference line at the 100% mark.

**Overview sparklines:** 60px wide, 20px tall, stroke-width 1.5
**Detail sparklines:** 200px wide, 40px tall, stroke-width 2

## Key Metrics per Client (Overview Cards)

Each card shows 4 sparklines. The specific metrics chosen per client are the most representative:

| Client | Metric 1 | Metric 2 | Metric 3 | Metric 4 |
|--------|----------|----------|----------|----------|
| Evergreen | Sessions | MQLs | Email Revenue | Hours vs SOW |
| Brightwash | Sessions | MQLs | Email Revenue | Hours vs SOW |
| Vello | Sessions | MQLs | Email Revenue | Hours vs SOW |
| Petal & Thorn | Sessions | MQLs | Email Revenue | Hours vs SOW |
| Kova | Sessions | MQLs | Email Revenue | Hours vs SOW |
| Drift Coffee | Sessions | Subscriptions | Email Revenue | Hours vs SOW |
| Summit Pet | Sessions | MQLs | Instagram | Hours vs SOW |
| Moonridge | Sessions | MQLs | Instagram | Hours vs SOW |

## Data Source

All data from `data/client_performance_history.md`:
- 6 months: October 2025 – March 2026
- 8 clients, each with 5 metric categories
- Each category has a data table with monthly values

## Visual Design

Matches existing tool conventions:
- **Header:** Dark bar (#1a1a1a) with white text
- **Background:** #f5f5f5
- **Cards:** White (#fff) with subtle shadow, left border color-coded
- **Typography:** System sans-serif, 13px base
- **Status colors:** Red (#dc3545), Yellow (#ffc107), Green (#28a745)
- **Tables:** Clean, compact, alternating subtle backgrounds, 12px font

## Technical Implementation

- **Single self-contained HTML file** — all CSS, SVG generation, and vanilla JavaScript inlined
- **Data embedded as JavaScript objects** — full 6 months of metrics for all 8 clients
- **SVG sparklines generated at render time** from data arrays
- **Two render modes:** `renderOverview()` for the grid, `renderDetail(clientId)` for the single-client view

## Out of Scope

- Real-time data fetching from GA/social/email APIs
- Date range selection (fixed to Oct 2025–Mar 2026)
- Cross-client comparison charts
- Export or download functionality
- Custom metric selection
