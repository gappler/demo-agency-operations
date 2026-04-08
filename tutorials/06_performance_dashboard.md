---
title: "Tutorial: Multi-Client Performance Dashboard"
tool_number: 6
version: 1.0
created: 2026-04-07
output_path: tools/performance_dashboard.html
---

# Tutorial 06: Multi-Client Performance Dashboard

## Goal

Build a single-page HTML dashboard that shows 8 agency clients in a 2x4 grid with SVG sparklines for 6-month metric trends, plus a click-to-expand detail view with full data tables.

## Step 1: Set Up the Prompt

The prompt needs to specify:
- Output file path (`tools/performance_dashboard.html`)
- Zero dependencies (self-contained HTML)
- Visual conventions matching existing tools (dark header, system fonts, card styles)
- All 8 clients with complete 6-month data arrays
- The sparkline function implementation
- Which metrics appear on which client's card
- Detail view structure with 5 sections and MoM change column

**Key decision:** Providing the complete data in the prompt ensures no guessing or truncation. The file will be large (~500 lines of JS data alone) but that is expected for a dashboard with 8 clients x 5 sections x 5-6 metrics each.

## Step 2: Data Architecture

Each client is a JS object with:
- Metadata: `id`, `name`, `health`, `healthColor`, `retainer`, `manager`
- `sparkMetrics`: array of 4 metric labels shown on the overview card
- 5 section objects: `traffic`, `leads`, `social`, `email`, `hours`
- Each section maps metric names to 6-element arrays (Oct 2025 - Mar 2026)

**Key decision:** Using metric name strings as object keys makes it easy to iterate for table generation. The `sparkMetrics` array per client handles the per-client customization (Drift shows Subscriptions instead of MQLs, Summit shows Instagram Followers, etc.).

## Step 3: Sparkline Function

The sparkline function takes `(data, width, height, color)` and returns an SVG string:
- Maps data points to x,y coordinates within a padded viewBox
- Uses a `<polyline>` with rounded line caps/joins
- Color is determined by comparing first vs last data point

```javascript
function sparkline(data, width, height, color) {
  const padding = 2;
  const w = width - padding * 2;
  const h = height - padding * 2;
  const min = Math.min(...data);
  const max = Math.max(...data);
  const range = max - min || 1;
  const points = data.map((v, i) => {
    const x = padding + (i / (data.length - 1)) * w;
    const y = padding + h - ((v - min) / range) * h;
    return `${x.toFixed(1)},${y.toFixed(1)}`;
  }).join(' ');
  return `<svg width="${width}" height="${height}" ...><polyline points="${points}" .../></svg>`;
}
```

## Step 4: Handling Moonridge (Pre-Contract Client)

Moonridge Spirits started in December 2025, so October and November data is zero for most metrics.

**Key decision:** For sparklines on the overview card, slice to only Dec-Mar (4 points) for Sessions, MQLs, and Hours Util. For the detail table, show all 6 months but render zero values as em-dashes.

## Step 5: Stat Card Calculations

All 4 stat cards are computed dynamically:
1. **Total Clients** — hardcoded 8
2. **Avg Monthly Sessions** — average of all clients' March (index 5) sessions
3. **Portfolio MQLs** — sum of March MQLs across all clients
4. **Avg SOW Utilization** — sum of March delivered hours / sum of March SOW hours (excluding zero-SOW clients)

## Step 6: Overview Grid

Cards sorted by `health` descending. Each card:
- 4px left border colored by health status
- Client name, retainer, health score with emoji
- Trend indicator computed from primary metric's first vs last value
- 2x2 grid of sparklines with label and latest value

## Step 7: Detail View

`showDetail(clientId)` hides the grid and renders:
- Header with name, retainer, health badge, trend
- Back button to return to overview
- 5 sections (Traffic, Leads, Social, Email, Hours), each with:
  - Larger sparkline (200x40) of the primary metric
  - Full data table with all metrics across all 6 months
  - MoM change column comparing Feb vs Mar values

## Step 8: Commit

```
git add tools/performance_dashboard.html tutorials/06_performance_dashboard.md docs/process_log.md
git commit -m "Add multi-client performance dashboard with sparklines and detail views"
```

## Final Output

- **File:** `tools/performance_dashboard.html`
- **Size:** ~600 lines, self-contained HTML/CSS/JS
- **Features:** 8 client cards with sparklines, click-to-detail with full data tables, responsive stat cards
- **Data:** Complete 6-month history for all 8 clients across 5 metric categories
