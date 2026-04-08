# Resource Allocator Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an interactive resource allocation matrix for Sidebar Creative that shows projects as rows, team members as columns, with click-to-edit hour cells and real-time capacity tracking.

**Architecture:** Single self-contained HTML file with embedded data, CSS, and vanilla JavaScript. Projects as rows, team members as columns filtered by department tabs. Click-to-edit cells with over-allocation warnings. Matches the visual conventions of the existing project timeline dashboard.

**Tech Stack:** HTML, CSS, vanilla JavaScript — zero external dependencies.

**Spec:** `docs/superpowers/specs/2026-04-07-resource-allocator-design.md`

**Data sources:**
- `brand/brand_definition.md` — Team roster (37 billable staff), client portfolio, primary team assignments
- `data/client_sow_terms.md` — SOW hour allocations by discipline per client

---

### Task 1: HTML Scaffold with Header, Stats, and Filter Bar

**Files:**
- Create: `tools/resource_allocator.html`

Build the page shell — everything above the matrix table.

- [ ] **Step 1: Create the HTML file with document structure, CSS reset, and header**

Create `tools/resource_allocator.html` with the full document structure. Include:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Resource Allocator — Sidebar Creative</title>
<style>
/* Reset & Base — match project_timeline.html */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { font-size: 14px; }
body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f5f5f5;
  color: #222;
  min-width: 1200px;
}

/* Header */
.header {
  background: #1a1a1a;
  color: #fff;
  padding: 14px 28px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.header-left { display: flex; align-items: baseline; gap: 18px; }
.header-brand { font-size: 1.25rem; font-weight: 700; letter-spacing: .02em; }
.header-title { font-size: .95rem; font-weight: 400; color: #aaa; }
.header-date { font-size: .85rem; color: #777; }
</style>
</head>
<body>
<div class="header">
  <div class="header-left">
    <div class="header-brand">Sidebar Creative</div>
    <div class="header-title">Resource Allocator</div>
  </div>
  <div class="header-date" id="headerDate"></div>
</div>
</body>
</html>
```

- [ ] **Step 2: Add the summary stat cards CSS and HTML**

Add CSS (after header styles):

```css
/* Stats Row */
.stats-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
  padding: 18px 28px;
}
.stat-card {
  background: #fff;
  border-radius: 6px;
  padding: 16px 20px;
  box-shadow: 0 1px 3px rgba(0,0,0,.08);
}
.stat-label { font-size: .75rem; text-transform: uppercase; letter-spacing: .06em; color: #777; margin-bottom: 4px; }
.stat-value { font-size: 1.6rem; font-weight: 700; }
.stat-value.red { color: #dc3545; }
.stat-value.green { color: #28a745; }
```

Add HTML after the header div:

```html
<div class="stats-row">
  <div class="stat-card">
    <div class="stat-label">Overloaded (&gt;115%)</div>
    <div class="stat-value red" id="statOverloaded">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Underutilized (&lt;70%)</div>
    <div class="stat-value green" id="statUnderutilized">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Total Hrs/Week</div>
    <div class="stat-value" id="statTotalHrs">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Avg Utilization</div>
    <div class="stat-value" id="statAvgUtil">—</div>
  </div>
</div>
```

- [ ] **Step 3: Add department filter tabs and week/month toggle**

Add CSS:

```css
/* Filter Bar */
.filter-bar {
  padding: 0 28px 14px;
  display: flex;
  align-items: center;
  gap: 8px;
}
.filter-btn {
  border: 1px solid #ccc;
  background: #fff;
  padding: 6px 16px;
  border-radius: 4px;
  cursor: pointer;
  font-size: .85rem;
  font-family: inherit;
  transition: background .15s, border-color .15s;
}
.filter-btn:hover { background: #eee; }
.filter-btn.active { background: #1a1a1a; color: #fff; border-color: #1a1a1a; }
.toggle-group {
  margin-left: auto;
  display: flex;
  gap: 0;
}
.toggle-btn {
  border: 1px solid #ccc;
  background: #fff;
  padding: 6px 12px;
  cursor: pointer;
  font-size: .85rem;
  font-family: inherit;
  transition: background .15s;
}
.toggle-btn:first-child { border-radius: 4px 0 0 4px; }
.toggle-btn:last-child { border-radius: 0 4px 4px 0; border-left: 0; }
.toggle-btn.active { background: #1a1a1a; color: #fff; border-color: #1a1a1a; }
```

Add HTML after stats-row:

```html
<div class="filter-bar">
  <button class="filter-btn active" data-dept="all">All Depts</button>
  <button class="filter-btn" data-dept="design">Design (12)</button>
  <button class="filter-btn" data-dept="development">Development (8)</button>
  <button class="filter-btn" data-dept="strategy">Strategy (4)</button>
  <button class="filter-btn" data-dept="content">Content (3)</button>
  <button class="filter-btn" data-dept="account">Account/PM (6)</button>
  <div class="toggle-group">
    <button class="toggle-btn active" data-period="week">Week</button>
    <button class="toggle-btn" data-period="month">Month</button>
  </div>
</div>
```

- [ ] **Step 4: Open in browser and verify the header, stat cards, and filter tabs render correctly**

Run: `open tools/resource_allocator.html`

Expected: Dark header with "Sidebar Creative — Resource Allocator", four stat card placeholders showing "—", department filter tabs with "All Depts" highlighted, week/month toggle with "Week" highlighted.

- [ ] **Step 5: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Add resource allocator scaffold with header, stats, filter bar"
```

---

### Task 2: Embed All Data as JavaScript Objects

**Files:**
- Modify: `tools/resource_allocator.html`

Add the complete data layer inside a `<script>` tag: team roster, project list, and allocation matrix. All data derived from `brand/brand_definition.md` and `data/client_sow_terms.md`.

- [ ] **Step 1: Add the team roster data**

Add a `<script>` tag before `</body>` with the full team roster array. Each entry includes `id`, `name`, `initials`, `role`, `dept`, `capacity` (always 40), `billableTarget`, and `currentLoad`. Include all 37 billable staff. Exclude Leadership (4) and Operations/Admin (3).

Departments and billable targets:
- `"account"`: 32 hrs — Sophie Morales, Tomás Reyes, Alina Park, James Whitfield, Priya Nair, Casey Liu
- `"design"`: 34 hrs — Nico Strand, Yuki Tanaka, Bea Cortez, Finn Gallagher, Ava Lindström, Jordan Tate, Zara Osei, Milo Varga, Iris Chung, Kai Bergström, Luna Esperanza, Sam Delacroix
- `"development"`: 34 hrs — Owen Marsh, Diana Kovač, Leo Farias, Nadia Volkov, Ravi Subramanian, Elodie Blanc, Marcus Webb, Hana Yoshida
- `"strategy"`: 30 hrs — Celeste Durand, Andre Kim, Tessa Moreno, Wes Calloway
- `"content"`: 34 hrs — Rowan Griffiths, Jia Liang, Elliot Saunders

Example structure:

```javascript
const TEAM = [
  { id: 'sophie_morales', name: 'Sophie Morales', initials: 'SM', role: 'Account Director', dept: 'account', capacity: 40, billableTarget: 32, currentLoad: 42 },
  // ... all 37 entries
];
```

Use the exact `currentLoad` values from `brand/brand_definition.md` team roster tables.

- [ ] **Step 2: Add the projects data**

Add the projects array after TEAM. Include all 8 retainers and 4 project-based engagements. Each entry includes `id`, `name`, `type` ("retainer" or "project"), `value` (monthly retainer or total budget), `health` ("green", "yellow", or "red"), and for projects: `burned` percentage and `status`.

```javascript
const PROJECTS = [
  { id: 'evergreen', name: 'Evergreen Outdoor Co.', type: 'retainer', value: 45000, health: 'green' },
  { id: 'brightwash', name: 'Brightwash Home', type: 'retainer', value: 38000, health: 'green' },
  { id: 'vello', name: 'Vello Cycling', type: 'retainer', value: 32000, health: 'red' },
  { id: 'petal', name: 'Petal & Thorn', type: 'retainer', value: 28000, health: 'green' },
  { id: 'kova', name: 'Kova Skincare', type: 'retainer', value: 25000, health: 'yellow' },
  { id: 'drift', name: 'Drift Coffee Roasters', type: 'retainer', value: 22000, health: 'green' },
  { id: 'summit', name: 'Summit Pet Supply', type: 'retainer', value: 18000, health: 'green' },
  { id: 'moonridge', name: 'Moonridge Spirits', type: 'retainer', value: 12000, health: 'yellow' },
  { id: 'river_district', name: 'River District Rebrand', type: 'project', value: 85000, health: 'green', burned: 73, status: 'On track' },
  { id: 'cascade', name: 'Cascade Athletic Website', type: 'project', value: 120000, health: 'green', burned: 40, status: 'On track' },
  { id: 'fieldwork', name: 'Fieldwork Provisions Launch', type: 'project', value: 95000, health: 'green', burned: 19, status: 'Early stage' },
  { id: 'nw_natural', name: 'NW Natural Rebrand', type: 'project', value: 55000, health: 'yellow', burned: 75, status: 'Over scope' },
];
```

- [ ] **Step 3: Add the allocation matrix**

Add the weekly allocation matrix as a nested object: `ALLOCATIONS[projectId][teamMemberId] = weeklyHours`. Derive weekly hours from SOW monthly hours / 4.33, rounded to nearest integer. Map SOW discipline hours to primary team members listed in client details in `brand/brand_definition.md`.

The primary team assignments from the brand definition are:

- **Evergreen:** Sophie (AM, 20/4.33=5), Nico (design, 80/4.33=18), Owen (dev, 60/4.33=14), Celeste (strategy, 24/4.33=6), Rowan (content, 40/4.33=9)
- **Brightwash:** Tomás (AM, 18/4.33=4), Yuki (design, 65/4.33=15), Diana (dev, 55/4.33=13), Andre (strategy, 16/4.33=4), Jia (content, 35/4.33=8)
- **Vello:** Sophie (AM, 16/4.33=4), Bea (design, 60/4.33=14), Leo (dev, 42/4.33=10), Celeste (strategy, 20/4.33=5), Elliot (content, 25/4.33=6)
- **Petal:** Alina (AM, 14/4.33=3), Finn (design, 55/4.33=13), Hana (dev, 40/4.33=9), Tessa (strategy, 12/4.33=3), Jia (content, 30/4.33=7)
- **Kova:** Tomás (AM, 14/4.33=3), Jordan (design, 45/4.33=10), Nadia (dev, 38/4.33=9), Andre (strategy, 16/4.33=4), Rowan (content, 18/4.33=4)
- **Drift:** Priya (PM, 10/4.33=2), Luna (design, 40/4.33=9), Ravi (dev, 38/4.33=9), Wes (strategy, 10/4.33=2), Rowan (content, 24/4.33=6)
- **Summit:** James (PM, 10/4.33=2), Zara (design, 35/4.33=8), Marcus (dev, 30/4.33=7), Tessa (strategy, 8/4.33=2), Jia (content, 20/4.33=5)
- **Moonridge:** Alina (AM, 8/4.33=2), Milo (design, 28/4.33=6), Elodie (dev, 16/4.33=4), Elliot (content, 14/4.33=3)

For the 4 project-based engagements, distribute hours across team members to fill remaining currentLoad gaps. Use the known currentLoad totals to derive reasonable allocations:

- **River District Rebrand:** Nico (design, 8), Kai (UX, 8), Luna (design, 4), Owen (dev, 4)
- **Cascade Athletic Website:** Yuki (design, 8), Bea (design, 6), Owen (dev, 12), Diana (dev, 8), Iris (motion, 4)
- **Fieldwork Provisions Launch:** Sam (design, 6), Ava (design, 10), Leo (dev, 6), Nadia (dev, 4), Elliot (content, 4)
- **NW Natural Rebrand:** Nico (design, 10), Finn (design, 8), Kai (UX, 6), Hana (dev, 4), Celeste (strategy, 4)

Add extra secondary assignments to fill remaining currentLoad gaps for team members not yet fully allocated (e.g., additional small allocations of 2-4 hrs to Ava, Jordan, Zara, Marcus, etc. across retainers they're supporting).

Structure:

```javascript
// Mutable allocation matrix — weeklyHours per project per person
const allocations = {};
PROJECTS.forEach(p => { allocations[p.id] = {}; });
allocations['evergreen']['sophie_morales'] = 5;
allocations['evergreen']['nico_strand'] = 18;
// ... all assignments
```

- [ ] **Step 4: Add utility constants and helper functions**

```javascript
const HEALTH_COLORS = { green: '#28a745', yellow: '#ffc107', red: '#dc3545' };
const HEALTH_LABELS = { green: 'Healthy', yellow: 'At Risk', red: 'Critical' };
const WEEK_MONTH_FACTOR = 4.33;

function getPersonTotal(personId) {
  let total = 0;
  for (const projId in allocations) {
    total += (allocations[projId][personId] || 0);
  }
  return total;
}

function getUtilization(personId) {
  const person = TEAM.find(t => t.id === personId);
  return person ? getPersonTotal(personId) / person.billableTarget : 0;
}

function getStatusColor(util) {
  if (util > 1.15) return '#dc3545';
  if (util < 0.70) return '#28a745';
  return '#555';
}

function formatMoney(n) {
  return n >= 1000 ? '$' + Math.round(n / 1000) + 'K' : '$' + n;
}
```

- [ ] **Step 5: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Add embedded data layer: team roster, projects, allocation matrix"
```

---

### Task 3: Render the Matrix Table

**Files:**
- Modify: `tools/resource_allocator.html`

Build the `renderMatrix()` function that generates the full table from data.

- [ ] **Step 1: Add the matrix container CSS**

Add to the `<style>` block:

```css
/* Matrix Container */
.matrix-wrap {
  padding: 0 28px 28px;
}
.matrix-scroll {
  overflow: auto;
  max-height: calc(100vh - 220px);
  background: #fff;
  border-radius: 6px;
  box-shadow: 0 1px 3px rgba(0,0,0,.08);
}
.matrix-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}

/* Sticky project column */
.matrix-table th.project-col,
.matrix-table td.project-col {
  position: sticky;
  left: 0;
  z-index: 2;
  min-width: 200px;
  max-width: 220px;
  background: inherit;
}
.matrix-table thead th.project-col { z-index: 3; }

/* Sticky footer row */
.matrix-table tfoot td,
.matrix-table tfoot th {
  position: sticky;
  bottom: 0;
  z-index: 2;
}
.matrix-table tfoot th.project-col { z-index: 3; }

/* Header cells */
.matrix-table thead th {
  background: #1a1a1a;
  color: #fff;
  padding: 8px 4px;
  text-align: center;
  font-weight: 600;
  font-size: 11px;
  vertical-align: bottom;
  min-width: 72px;
}
.matrix-table thead th.project-col {
  text-align: left;
  padding: 10px 12px;
  font-size: 12px;
  background: #1a1a1a;
}

/* Column header internals */
.col-role { font-size: 10px; font-weight: 400; opacity: 0.7; }
.col-name { font-size: 11px; margin-top: 1px; }
.col-bar { width: 40px; height: 4px; background: rgba(255,255,255,0.2); border-radius: 2px; margin: 3px auto 0; overflow: visible; position: relative; }
.col-bar-fill { height: 100%; border-radius: 2px; }
.col-util { font-size: 9px; margin-top: 1px; }

/* Section header rows */
.section-row td {
  background: #f0f0f0;
  padding: 6px 12px;
  font-weight: 700;
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: #555;
}

/* Project info in left column */
.project-name { font-weight: 600; font-size: 13px; }
.project-meta { display: flex; align-items: center; gap: 6px; margin-top: 2px; }
.project-value { font-size: 10px; color: #888; }
.health-dot { width: 6px; height: 6px; border-radius: 50%; display: inline-block; }
.health-label { font-size: 10px; }

/* Hour cells */
.hour-cell {
  padding: 4px;
  text-align: center;
  cursor: pointer;
  transition: background .1s;
}
.hour-cell:hover { background: #f0f0f0; }
.hour-val {
  border-radius: 4px;
  padding: 5px 0;
  font-weight: 700;
  font-size: 13px;
}
.hour-empty {
  background: #f8f8f8;
  border: 1px solid #eee;
  border-radius: 4px;
  padding: 5px 0;
  color: #ccc;
  font-size: 13px;
}
.hour-empty:hover { color: #999; }
.hour-empty:hover::after { content: '+'; }

/* Total column */
.row-total {
  padding: 4px 8px;
  text-align: center;
  font-weight: 600;
  font-size: 13px;
  color: #555;
  min-width: 55px;
}

/* Footer cells */
.matrix-table tfoot td,
.matrix-table tfoot th {
  background: #1a1a1a;
  color: #fff;
  padding: 8px 4px;
  text-align: center;
}
.matrix-table tfoot th {
  text-align: left;
  padding: 10px 12px;
  font-weight: 700;
  font-size: 12px;
}
.footer-total { font-weight: 700; font-size: 14px; }
.footer-target { font-size: 10px; opacity: 0.6; }
```

- [ ] **Step 2: Add the matrix container HTML**

Add after the filter-bar div:

```html
<div class="matrix-wrap">
  <div class="matrix-scroll" id="matrixScroll">
    <table class="matrix-table" id="matrixTable">
      <thead id="matrixHead"></thead>
      <tbody id="matrixBody"></tbody>
      <tfoot id="matrixFoot"></tfoot>
    </table>
  </div>
</div>
```

- [ ] **Step 3: Write the renderMatrix() function**

Add to the script block. This function reads the current department filter and period toggle, then builds `<thead>`, `<tbody>`, and `<tfoot>` content.

```javascript
let activeDept = 'all';
let activePeriod = 'week';

function renderMatrix() {
  const multiplier = activePeriod === 'month' ? WEEK_MONTH_FACTOR : 1;
  const visibleTeam = activeDept === 'all' ? TEAM : TEAM.filter(t => t.dept === activeDept);

  // ── THEAD ──
  let headHtml = '<tr><th class="project-col">Project</th>';
  visibleTeam.forEach(person => {
    const util = getUtilization(person.id);
    const color = getStatusColor(util);
    const pct = Math.round(util * 100);
    const barWidth = Math.min(100, pct);
    headHtml += `<th>
      <div class="col-role">${person.role}</div>
      <div class="col-name">${abbreviateName(person.name)}</div>
      <div class="col-bar"><div class="col-bar-fill" style="width:${barWidth}%;background:${color};"></div></div>
      <div class="col-util" style="color:${color === '#555' ? 'rgba(255,255,255,0.7)' : color};">${pct}%</div>
    </th>`;
  });
  headHtml += '<th style="min-width:55px;">Total</th></tr>';
  document.getElementById('matrixHead').innerHTML = headHtml;

  // ── TBODY ──
  let bodyHtml = '';
  const retainers = PROJECTS.filter(p => p.type === 'retainer');
  const projects = PROJECTS.filter(p => p.type === 'project');

  bodyHtml += renderSection('Retainer Clients', retainers, visibleTeam, multiplier);
  bodyHtml += renderSection('Project-Based', projects, visibleTeam, multiplier);
  document.getElementById('matrixBody').innerHTML = bodyHtml;

  // ── TFOOT ──
  let footHtml = '<tr><th class="project-col">TOTAL ALLOCATED</th>';
  visibleTeam.forEach(person => {
    const total = Math.round(getPersonTotal(person.id) * multiplier);
    const target = Math.round(person.billableTarget * multiplier);
    const util = getUtilization(person.id);
    const color = getStatusColor(util);
    const textColor = color === '#555' ? '#fff' : (color === '#28a745' ? '#6fdc6f' : '#ff6b6b');
    footHtml += `<td>
      <div class="footer-total" style="color:${textColor};">${total}</div>
      <div class="footer-target">/ ${target} target</div>
    </td>`;
  });
  // Grand total
  const grandTotal = Math.round(visibleTeam.reduce((sum, p) => sum + getPersonTotal(p.id), 0) * multiplier);
  footHtml += `<td><div class="footer-total">${grandTotal}</div></td>`;
  footHtml += '</tr>';
  document.getElementById('matrixFoot').innerHTML = footHtml;

  // ── Update stats ──
  updateStats(multiplier);
}

function renderSection(title, projectList, visibleTeam, multiplier) {
  let html = `<tr class="section-row"><td colspan="${visibleTeam.length + 2}">${title}</td></tr>`;
  projectList.forEach(proj => {
    html += '<tr style="border-bottom:1px solid #eee;">';
    // Project info cell
    html += `<td class="project-col" style="padding:8px 12px;background:#fff;">
      <div class="project-name">${proj.name}</div>
      <div class="project-meta">
        <span class="project-value">${proj.type === 'retainer' ? formatMoney(proj.value) + '/mo' : formatMoney(proj.value) + ' budget'}</span>
        <span class="health-dot" style="background:${HEALTH_COLORS[proj.health]};"></span>
        <span class="health-label" style="color:${HEALTH_COLORS[proj.health]};">${proj.type === 'retainer' ? HEALTH_LABELS[proj.health] : (proj.burned + '% burned')}</span>
      </div>
    </td>`;
    // Hour cells
    let rowTotal = 0;
    visibleTeam.forEach(person => {
      const raw = allocations[proj.id][person.id] || 0;
      const display = Math.round(raw * multiplier);
      rowTotal += raw;
      const util = getUtilization(person.id);
      const color = getStatusColor(util);
      if (raw > 0) {
        html += `<td class="hour-cell" data-proj="${proj.id}" data-person="${person.id}">
          <div class="hour-val" style="background:${color}15;border:1px solid ${color}30;color:${color};">${display}</div>
        </td>`;
      } else {
        html += `<td class="hour-cell" data-proj="${proj.id}" data-person="${person.id}">
          <div class="hour-empty">—</div>
        </td>`;
      }
    });
    html += `<td class="row-total">${Math.round(rowTotal * multiplier)}</td>`;
    html += '</tr>';
  });
  return html;
}

function abbreviateName(fullName) {
  const parts = fullName.split(' ');
  return parts[0] + ' ' + parts[parts.length - 1][0] + '.';
}

function updateStats(multiplier) {
  let overloaded = 0, underutilized = 0, totalHrs = 0, utilSum = 0;
  TEAM.forEach(person => {
    const util = getUtilization(person.id);
    if (util > 1.15) overloaded++;
    if (util < 0.70) underutilized++;
    totalHrs += getPersonTotal(person.id);
    utilSum += util;
  });
  document.getElementById('statOverloaded').textContent = overloaded;
  document.getElementById('statUnderutilized').textContent = underutilized;
  document.getElementById('statTotalHrs').textContent = Math.round(totalHrs * multiplier).toLocaleString();
  document.getElementById('statAvgUtil').textContent = Math.round(utilSum / TEAM.length * 100) + '%';
}
```

- [ ] **Step 4: Wire up filter tabs, toggle, and initial render**

Add to the end of the script block:

```javascript
// Set current date in header
document.getElementById('headerDate').textContent = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });

// Department filter tabs
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activeDept = btn.dataset.dept;
    renderMatrix();
  });
});

// Week/Month toggle
document.querySelectorAll('.toggle-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.toggle-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activePeriod = btn.dataset.period;
    renderMatrix();
  });
});

// Initial render
renderMatrix();
```

- [ ] **Step 5: Open in browser and verify the full matrix renders**

Run: `open tools/resource_allocator.html`

Expected: Full matrix with 12 project rows (8 retainers + 4 project-based), team member columns with capacity bars in header, hour cells populated, sticky project column and footer row. Department tabs filter columns. Week/Month toggle recalculates all values.

- [ ] **Step 6: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Render resource allocation matrix with department filters and week/month toggle"
```

---

### Task 4: Click-to-Edit Hour Cells

**Files:**
- Modify: `tools/resource_allocator.html`

Add inline editing: click a cell to type a new hour value. Enter/Tab confirms, Escape cancels. All totals and capacity bars recalculate on confirm.

- [ ] **Step 1: Add editing CSS**

Add to the style block:

```css
/* Inline edit */
.hour-input {
  width: 48px;
  padding: 4px;
  text-align: center;
  font-size: 13px;
  font-weight: 700;
  font-family: inherit;
  border: 2px solid #007bff;
  border-radius: 4px;
  outline: none;
  background: #fff;
}
```

- [ ] **Step 2: Add the click-to-edit event handler**

Add to the script block, before the initial `renderMatrix()` call:

```javascript
document.getElementById('matrixTable').addEventListener('click', function(e) {
  const cell = e.target.closest('.hour-cell');
  if (!cell || cell.querySelector('.hour-input')) return;

  const projId = cell.dataset.proj;
  const personId = cell.dataset.person;
  const currentVal = allocations[projId][personId] || 0;
  const multiplier = activePeriod === 'month' ? WEEK_MONTH_FACTOR : 1;
  const displayVal = Math.round(currentVal * multiplier);

  const input = document.createElement('input');
  input.type = 'number';
  input.className = 'hour-input';
  input.value = displayVal || '';
  input.min = 0;
  input.max = 60;

  cell.innerHTML = '';
  cell.appendChild(input);
  input.focus();
  input.select();

  function commitEdit() {
    const rawInput = parseInt(input.value) || 0;
    const weeklyVal = activePeriod === 'month' ? Math.round(rawInput / WEEK_MONTH_FACTOR) : rawInput;
    const newVal = Math.max(0, weeklyVal);

    const person = TEAM.find(t => t.id === personId);
    const oldVal = allocations[projId][personId] || 0;
    const newTotal = getPersonTotal(personId) - oldVal + newVal;
    const newUtil = newTotal / person.billableTarget;

    if (newUtil > 1.15 && newVal > oldVal) {
      showOverloadWarning(cell, projId, personId, person, oldVal, newVal, newUtil);
    } else {
      applyEdit(projId, personId, newVal);
    }
  }

  input.addEventListener('keydown', function(e) {
    if (e.key === 'Enter' || e.key === 'Tab') {
      e.preventDefault();
      commitEdit();
    } else if (e.key === 'Escape') {
      renderMatrix();
    }
  });

  input.addEventListener('blur', function() {
    // Small delay to allow warning buttons to be clicked
    setTimeout(() => {
      if (!cell.querySelector('.overload-warning')) {
        commitEdit();
      }
    }, 150);
  });
});

function applyEdit(projId, personId, newVal) {
  if (newVal > 0) {
    allocations[projId][personId] = newVal;
  } else {
    delete allocations[projId][personId];
  }
  renderMatrix();
}
```

- [ ] **Step 3: Verify click-to-edit works**

Open the page, click an hour cell, type a new value, press Enter. Verify:
- The cell updates with the new value
- The row total updates
- The footer total for that person updates
- The column header capacity bar and percentage update
- The stat cards update

Also test: click an empty cell (—), type a value, press Enter. Verify it becomes a populated cell.

Also test: click a cell, press Escape. Verify the original value is restored.

- [ ] **Step 4: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Add click-to-edit for hour cells with real-time recalculation"
```

---

### Task 5: Over-Allocation Warning

**Files:**
- Modify: `tools/resource_allocator.html`

When an edit would push someone past 115%, show an inline warning with confirmation and alternative suggestions.

- [ ] **Step 1: Add warning CSS**

```css
/* Overload Warning */
.overload-warning {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  background: #fff;
  border: 2px solid #dc3545;
  border-radius: 6px;
  padding: 10px 14px;
  box-shadow: 0 4px 12px rgba(0,0,0,.15);
  z-index: 10;
  white-space: nowrap;
  font-size: 12px;
  min-width: 240px;
}
.overload-warning .warn-text {
  color: #dc3545;
  font-weight: 600;
  margin-bottom: 6px;
}
.overload-warning .warn-suggest {
  color: #28a745;
  font-size: 11px;
  margin-bottom: 8px;
}
.overload-warning .warn-buttons {
  display: flex;
  gap: 6px;
}
.warn-btn {
  padding: 4px 12px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
  font-size: 12px;
  font-family: inherit;
}
.warn-btn.confirm { background: #dc3545; color: #fff; }
.warn-btn.cancel { background: #eee; color: #555; }

/* Make hour-cell position relative for warning positioning */
.hour-cell { position: relative; }
```

- [ ] **Step 2: Write the showOverloadWarning function**

```javascript
function showOverloadWarning(cell, projId, personId, person, oldVal, newVal, newUtil) {
  // Find up to 2 underutilized alternatives in the same dept
  const alternatives = TEAM
    .filter(t => t.dept === person.dept && t.id !== personId && getUtilization(t.id) < 0.70)
    .slice(0, 2)
    .map(t => {
      const free = Math.round(t.billableTarget - getPersonTotal(t.id));
      return `${t.name} (${Math.round(getUtilization(t.id) * 100)}%, ${free} hrs free)`;
    });

  let suggestHtml = '';
  if (alternatives.length > 0) {
    suggestHtml = `<div class="warn-suggest">Available: ${alternatives.join(', ')}</div>`;
  }

  const warning = document.createElement('div');
  warning.className = 'overload-warning';
  warning.innerHTML = `
    <div class="warn-text">${person.name.split(' ')[0]} will be at ${Math.round(newUtil * 100)}% — confirm?</div>
    ${suggestHtml}
    <div class="warn-buttons">
      <button class="warn-btn confirm" onclick="applyEdit('${projId}', '${personId}', ${newVal})">Confirm</button>
      <button class="warn-btn cancel" onclick="renderMatrix()">Cancel</button>
    </div>
  `;

  cell.innerHTML = '';
  const display = activePeriod === 'month' ? Math.round(newVal * WEEK_MONTH_FACTOR) : newVal;
  cell.innerHTML = `<div class="hour-val" style="background:#dc354515;border:1px solid #dc354530;color:#dc3545;">${display}</div>`;
  cell.appendChild(warning);
}
```

- [ ] **Step 3: Verify warning works**

Open the page, find someone near their limit (e.g., Nico at 141%). Add or increase hours on a project for them. Verify:
- The warning popover appears below the cell
- It shows the projected percentage
- It suggests underutilized alternatives in the same department (e.g., Ava Lindström for Design)
- Clicking "Confirm" applies the edit and dismisses the warning
- Clicking "Cancel" reverts and dismisses

Also test: edit someone who's not overloaded. Verify no warning appears.

- [ ] **Step 4: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Add over-allocation warning with confirmation and alternative suggestions"
```

---

### Task 6: Polish and Final Verification

**Files:**
- Modify: `tools/resource_allocator.html`

Final cleanup: ensure edge cases are handled, visual polish matches the timeline dashboard.

- [ ] **Step 1: Add responsive scroll behavior and edge case handling**

Add debounced resize handler and ensure the warning popover stays within viewport bounds:

```javascript
// Close any open warnings on scroll
document.getElementById('matrixScroll').addEventListener('scroll', function() {
  const warning = document.querySelector('.overload-warning');
  if (warning) renderMatrix();
});

// Debounced resize
let resizeTimer;
window.addEventListener('resize', function() {
  clearTimeout(resizeTimer);
  resizeTimer = setTimeout(renderMatrix, 200);
});
```

Also add a check in `showOverloadWarning` to flip the popover above the cell if it would overflow the viewport bottom:

```javascript
// After appending warning to cell, check bounds
requestAnimationFrame(() => {
  const rect = warning.getBoundingClientRect();
  if (rect.bottom > window.innerHeight) {
    warning.style.top = 'auto';
    warning.style.bottom = '100%';
  }
  if (rect.left < 0) {
    warning.style.left = '0';
    warning.style.transform = 'none';
  }
  if (rect.right > window.innerWidth) {
    warning.style.left = 'auto';
    warning.style.right = '0';
    warning.style.transform = 'none';
  }
});
```

- [ ] **Step 2: Verify the complete tool end-to-end**

Open `tools/resource_allocator.html` in the browser. Walk through this verification checklist:

1. Header shows "Sidebar Creative — Resource Allocator" with today's date
2. Four stat cards show correct counts (5 overloaded, 4 underutilized)
3. "All Depts" tab shows all 37 columns — table scrolls horizontally, project column stays pinned
4. Click "Design (12)" — only 12 designer columns shown
5. Click "Development (8)" — only 8 dev columns shown
6. Click "Month" toggle — all values multiply by ~4.33
7. Click "Week" to return
8. Click an empty cell, type "5", press Enter — cell populates, totals update
9. Click a populated cell, change value, press Enter — cell updates, totals update
10. Push someone past 115% — warning appears with suggestion
11. Click "Cancel" on warning — reverts
12. Push past 115% again — click "Confirm" — edit applies
13. Scroll down — footer row stays visible
14. Scroll right — project column stays visible

- [ ] **Step 3: Commit**

```bash
git add tools/resource_allocator.html
git commit -m "Polish resource allocator: scroll handling, viewport-safe warnings, edge cases"
```

---

### Task 7: Process Log, Tutorial, and Final Commit

**Files:**
- Modify: `docs/process_log.md`
- Create: `tutorials/02_resource_allocator.md`

Document the tool per CLAUDE.md requirements.

- [ ] **Step 1: Prepend a log entry to docs/process_log.md**

Add at the top (after any YAML header), newest first:

```markdown
## 2026-04-07 — Resource Allocator

**Prompt:** Build a resource allocator for Sidebar Creative. Read the team roster and project data. Show each team member's current capacity — hours allocated vs. available — with visual indicators for overloaded (red) and underutilized (green) staff. Let me drag or reassign team members across projects and see capacity update in real time. Output as an interactive HTML file.

**Built:** Interactive resource allocation matrix — projects as rows, team members as columns, with click-to-edit hour cells, department filtering, week/month toggle, and over-allocation warnings.

**Key Decisions:**
- Projects-as-rows orientation (mirrors Monday meeting flow: walk through projects, not people)
- Click-to-edit over drag-and-drop (faster for real allocation work in meetings)
- Soft over-allocation constraint with confirmation + alternative suggestions
- Weekly default with monthly toggle (working unit vs. SOW review)
- Department filter tabs to manage 37-column width
- Sticky left column and footer row for scroll context

**Files Created/Modified:**
- `tools/resource_allocator.html` (created)
- `docs/superpowers/specs/2026-04-07-resource-allocator-design.md` (created)
- `docs/superpowers/plans/2026-04-07-resource-allocator.md` (created)
- `docs/process_log.md` (modified)
- `tutorials/02_resource_allocator.md` (created)
```

- [ ] **Step 2: Create the tutorial**

Create `tutorials/02_resource_allocator.md` with YAML header and step-by-step guide covering:
- The exact prompts used during brainstorming
- Key decisions: matrix orientation, click-to-edit vs drag, warning system, time period toggle
- Data derivation: how SOW monthly hours were converted to weekly per-person allocations
- The final output file path: `tools/resource_allocator.html`
- Written as a replication guide for someone starting from scratch

- [ ] **Step 3: Commit**

```bash
git add docs/process_log.md tutorials/02_resource_allocator.md
git commit -m "Add process log and tutorial for resource allocator"
```
