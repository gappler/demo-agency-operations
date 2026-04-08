---
title: "Tutorial 04: Status Report Generator"
tool: status_reports.html
version: 1.0
created: 2026-04-07
---

# Tutorial 04: Status Report Generator

## What This Tool Does

A two-panel report viewer for a creative agency's weekly client status reports. The left panel lists a leadership roll-up plus 8 individual client reports; the right panel renders the selected report with structured sections and a clipboard copy button.

---

## The Prompt Used

> You are implementing a status report generator for a creative agency. Create `tools/status_reports.html` — a single self-contained HTML file.
>
> A two-panel report viewer: left panel selects reports, right panel displays them. Match existing tools in this project (dark #1a1a1a header, system sans-serif, #f5f5f5 background, white cards with subtle shadows). Include all 8 clients with full data plus a Monday Leadership Roll-Up.

---

## Step-by-Step Walkthrough

### Step 1: Scaffold the HTML Shell

Start with the standard header pattern used by all tools in this project:

```html
<div class="header">
  <div class="header-left">
    <span class="header-brand">Sidebar Creative</span>
    <span class="header-title">Status Reports</span>
  </div>
  <span class="header-date" id="headerDate"></span>
</div>
```

Add 4 stat cards in a CSS grid below: Reports Generated (hardcoded 9), Red Accounts, Yellow Accounts, Avg Health Score. The latter three are populated by `updateStats()` on init.

### Step 2: Two-Panel Layout

Use flexbox with `gap: 14px` and `padding: 0 28px 28px`:

- `.selector-panel` — `flex: 0 0 300px`, `overflow-y: auto`, `max-height: calc(100vh - 180px)` so it scrolls independently
- `.viewer-panel` — `flex: 1`, `overflow-y: auto`

### Step 3: Selector Panel Structure

Three visual zones rendered by `renderSelector()`:

1. **Roll-up entry** — dark background (#1a1a1a), white text, blue left border when active
2. **Section label** — uppercase gray divider ("Client Reports")
3. **Client rows** — health dot (8px circle), name, retainer/mo on left; health number on right. Active item gets blue left border and tinted background

Key CSS:
```css
.rollup-item.active { border-left: 3px solid #007bff; }
.report-item.active { background: #007bff08; border-left: 3px solid #007bff; }
```

### Step 4: Client Data Structure

Each client object contains:
- Identity: `id`, `name`, `health` (0-100), `status` ('green'/'yellow'/'red'), `retainer`, `accountDirector`
- Content arrays: `progress`, `blockers`, `nextSteps`
- Budget object: `sowHours`, `actualHours`, `utilization`, `monthlyBudget`, `actualCost`, `budgetStatus`
- Metrics array: `{ name, value, change, direction }` where `direction` drives color independently of sign
- Optional: `metricsNote` for contextual disclaimer (used by Petal & Thorn)

### Step 5: Render Functions

**`renderViewer()`** — Branches on `selectedReport === 'rollup'` vs client id. For client reports, renders 5 sections: Progress, Blockers & Risks, Next Steps, Budget Health (table), Key Metrics (table).

**`renderRollup()`** — Three sections: Accounts Requiring Attention (with health badge + bullet highlights), Accounts On Track (table), Portfolio Summary (table).

**`updateStats()`** — Counts red/yellow clients, averages health scores, updates DOM elements by id.

**`selectReport(id)`** — Sets `selectedReport`, re-renders both panels.

### Step 6: Metric Change Colors

The `direction` field ('up'/'down'/'flat') drives CSS class, not the sign of the number. This handles cases like Cost per Lead where a negative number is good:

```javascript
let changeClass = 'metric-change-flat';
if (m.direction === 'up') changeClass = 'metric-change-up';   // green
else if (m.direction === 'down') changeClass = 'metric-change-down'; // red
```

### Step 7: Copy Report Button

`copyReport(id)` assembles a plain-text representation of the report (section headers, bullet points, tables as key:value lines) and writes it to the clipboard via `navigator.clipboard.writeText()`. Button text changes to "Copied!" for 2 seconds.

### Step 8: Initialization

```javascript
document.getElementById('headerDate').textContent = REPORT_DATE;
renderSelector();
renderViewer();
updateStats();
```

Default state is `selectedReport = 'rollup'` so the leadership roll-up displays on load.

---

## Key Decisions and Rationale

| Decision | Rationale |
|---|---|
| Roll-up appears above client list in selector | Leadership report is the primary use case; clients are secondary |
| `direction` field separate from change sign | Cost per Lead, CPL — lower is better; direction lets data express intent |
| `metricsNote` optional field | Only Petal & Thorn needs a caveat about seasonal normalization; others don't |
| Plain-text copy (not markdown) | Paste-ready for email, Slack, and Google Docs without formatting noise |
| `max-height: calc(100vh - 180px)` on selector | Keeps selector scrollable without pushing the page length |

---

## Final Output

**File path:** `/tools/status_reports.html`

Open in any browser — no server required. All data is self-contained in the `<script>` block.
