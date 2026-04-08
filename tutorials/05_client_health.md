---
title: "Tutorial 05: Client Health Scorer"
tool: client_health.html
version: 1.0
created: 2026-04-07
---

# Tutorial 05: Client Health Scorer

## What This Tool Does

A read-only client health scorecard that ranks 8 agency clients worst-first by composite score. Each client card shows a score circle, three dimension bars (Performance / Profitability / Relationship), and a risk/highlights box. Four stat cards summarize portfolio health at a glance.

---

## The Prompt

> Build a client health scorer tool at `tools/client_health.html` — a single self-contained HTML file with zero dependencies. A ranked list of 8 agency clients scored on health, sorted worst-first. Read-only scorecard (no editing).

The prompt included exact client data for all 8 clients, badge specs, bar coloring rules, stat card values, and commit message.

---

## Key Decisions and Rationale

### 1. Data structure as a JS array, sorted ascending by composite
All 8 clients are defined in a `const clients = [...]` array, ordered by `composite` score from 44 (worst) to 91 (best). This makes the render order self-documenting and easy to re-sort if needed.

### 2. Dimension bar colors are independent of composite status
The spec explicitly requires that a green client can have a red profitability bar. Each bar calls `dimColor(score)` on its own dimension score, not the client's composite status. This avoids misleading visual uniformity.

### 3. Score circle tinting
The 48px score circle uses `rgba(color, 0.15)` background with the full status color for the number. This creates a soft, color-coded anchor at the far left of each card without overwhelming the white card background.

### 4. Stats calculated dynamically
`updateStats()` derives all four stat card values from the data array:
- **Portfolio Health** — `Math.round(average of all composite scores)` → 74
- **Revenue at Risk** — sum of retainers where `status === 'yellow' || 'red'` → $69K/mo
- **Green / Yellow / Red** — counted from `status` field → 5 / 2 / 1
- **Avg Effective Rate** — `Math.round(average of effectiveRate)` → $164/hr

### 5. Risk box header text is data-driven
Each client has a `riskHeader` field set to either `'Risk Factors'` or `'Highlights'` so red/yellow clients use one label and green clients use another, rendered in the appropriate status color.

### 6. Visual conventions match existing tools
- Header: `#1a1a1a` dark background, system sans-serif font
- Background: `#f5f5f5`
- Cards: white, `box-shadow: 0 1px 3px rgba(0,0,0,.08)`, `border-radius: 6px`
- Status colors: red `#dc3545`, yellow `#ffc107`, green `#28a745`, blue `#007bff`

---

## Step-by-Step Replication Guide

### Step 1: Set up the HTML shell
Create `tools/client_health.html` with `<!DOCTYPE html>` and standard `<meta charset>` / viewport tags. No external dependencies — all CSS and JS are inline.

### Step 2: Write the CSS
Key sections:
- `.header` — dark bar with flex layout, brand on left, date on right
- `.stats-row` — 4-column CSS grid of `.stat-card` elements
- `.client-card` — white card with `border-left: 4px solid` tinted by status class (`red`, `yellow`, `green`)
- `.score-circle` — 48px round div, tinted background + bold score number
- `.dimensions` — 3-column CSS grid, each with label/score header row + 6px bar track
- `.risk-box` — tinted background block with uppercase header and bullet list

### Step 3: Define the client data array
Each client object includes: `name`, `composite`, `status`, `performance`, `profitability`, `relationship`, `retainer`, `director`, `effectiveRate`, `sowUtilization`, `badges` (array of `{label, color}`), `riskHeader`, and `points` (array of strings).

Sort the array by `composite` ascending so index 0 is the worst client.

### Step 4: Write `dimColor(score)`
```js
function dimColor(score) {
  if (score >= 80) return 'green';
  if (score >= 60) return 'yellow';
  return 'red';
}
```
Apply this to each dimension score, not to the composite.

### Step 5: Write `renderCards()`
Iterate `clients`, build each card's HTML with template literals:
1. Score circle div (class = `score-circle ${status}`)
2. Name block: bold name, meta line (retainer + director), rate/utilization line
3. Badges: map over `c.badges` array
4. Dimensions grid: 3 `.dim-block` elements, each with a filled bar at `width: ${score}%`
5. Risk box: tinted background, header, bulleted list

Append each card to `#cards-container`.

### Step 6: Write `updateStats()`
Calculate averages and sums from the data array; use `innerHTML` with inline `style="color:..."` for the distribution stat so each count renders in its own color.

### Step 7: Set the header date
```js
function setDate() {
  const now = new Date();
  document.getElementById('header-date').textContent =
    now.toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' });
}
```

### Step 8: Call everything on load
```js
setDate();
renderCards();
updateStats();
```
No `DOMContentLoaded` wrapper needed because the script tag is at the bottom of `<body>`.

---

## Final Output

- **File:** `tools/client_health.html`
- **Size:** ~1 file, zero dependencies
- **Clients ranked worst-first:** Vello Cycling (44) → Moonridge Spirits (60) → Kova Skincare (64) → Brightwash Home (78) → Drift Coffee Roasters (83) → Summit Pet Supply (84) → Petal & Thorn (86) → Evergreen Outdoor Co. (91)
