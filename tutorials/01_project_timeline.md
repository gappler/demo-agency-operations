---
title: "Tutorial: Project Timeline Dashboard"
version: 1.0
created: 2026-04-07
tool_number: 1
output_path: tools/project_timeline.html
---

# Tutorial 01 — Project Timeline Dashboard

A step-by-step guide to building an interactive Gantt-style project timeline dashboard as a single self-contained HTML file.

---

## Step 1: Prompt

The prompt that initiated this build:

> Create `tools/project_timeline.html` — a single self-contained HTML file with all CSS and JS inlined. This is an interactive Gantt-style project timeline dashboard for Sidebar Creative agency.

The prompt included detailed specifications for:
- Branded header (dark bar, agency name, page title, date)
- Summary stats row (4 cards)
- Filter bar (All / Retainers / Projects)
- Gantt timeline with bars, milestones, today line, renewal markers
- Detail panel with budget, health scores, milestones, team, risk notes
- Complete hardcoded data for 8 retainers and 4 projects
- Exact color codes, layout dimensions, and interaction behaviors

## Step 2: Architecture Decisions

**Single-file approach:** Everything lives in one HTML file — CSS in a `<style>` tag, JavaScript in a `<script>` tag, no external dependencies. This makes the tool portable and easy to demo.

**CSS Grid for Gantt:** The timeline uses a CSS Grid with `grid-template-columns: 220px repeat(12, 1fr)` — a fixed-width label column plus 12 equal-width month columns.

**Overlay positioning:** Bars, milestones, and markers are rendered as absolutely positioned elements overlaid on the grid rows. After the grid renders, JavaScript reads the bounding rects of each month cell and positions elements accordingly. This approach keeps the layout clean while allowing precise positioning.

**Re-render on resize:** Since bar positions depend on pixel measurements, the Gantt re-renders on `window.resize` to stay accurate.

## Step 3: Data Structure

All data is stored as JavaScript constants at the top of the script:

- `RETAINERS` array (8 items) — each with: id, name, retainer amount, SOW dates, health status, hours (allocated/actual), utilization %, effective rate, health scores object, team array, milestones array, renewal date, risks array
- `PROJECTS` array (4 items) — each with: id, name, client, description, budget, burned amount/%, start/end month indices, health status, team array, milestones array, risks array

Key difference: retainer bars span their SOW dates (clamped to the 2026 window), while project bars use explicit month indices.

## Step 4: Key Implementation Details

### Health Color Mapping
- Retainers: green = On Track, yellow = At Risk, red = Churn Risk
- Projects: green = On Track, yellow = Over Scope, blue = Early Stage

### Stats Calculation
- At-risk count: all engagements with health = red OR yellow (includes over-scope projects)
- On-track count: all engagements with health = green OR blue
- Next deadline: scans all upcoming milestones, finds the nearest future date

### Bar Rendering
1. For retainers: parse SOW start/end dates, clamp to Jan–Dec 2026
2. For projects: use startMonth/endMonth indices directly
3. Progress fill width = utilization % (retainers) or burn % (projects)
4. Add overflow arrows if SOW extends before Jan 2026 or after Dec 2026

### Milestone Markers
- Positioned at the correct day within the month cell
- Color-coded: green (done), yellow (in-progress), gray (upcoming)
- Rendered as rotated squares (diamonds)

### Today Line
- Red vertical line at April 7, 2026
- Drawn on every row for visual alignment

### Detail Panel
- Opens below the Gantt when a row is clicked
- 2-column grid layout with budget, scores, milestones, team sections
- Full-width risk notes section at the bottom
- Close button or click same row to collapse

## Step 5: Final Output

The finished file is at: `tools/project_timeline.html`

Open it in any modern browser. No build step, no server needed — just open the HTML file directly.

## Step 6: Replication Checklist

To replicate this from scratch:

1. Create the HTML skeleton with a `<style>` and `<script>` tag
2. Build the header bar with dark background and agency branding
3. Add the stats row as a 4-column CSS Grid
4. Add the filter bar with three toggle buttons
5. Define the data arrays (retainers + projects) as JS constants
6. Build the Gantt grid using CSS Grid (label + 12 months)
7. After rendering the grid, overlay bars using absolute positioning
8. Add milestone diamonds and renewal markers by calculating pixel offsets
9. Draw the today line on each row
10. Implement click-to-expand detail panel
11. Add hover tooltips
12. Wire up filter buttons to show/hide sections
13. Add resize listener to re-render on window size change
