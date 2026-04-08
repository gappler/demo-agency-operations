# Capacity Planner Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an interactive capacity planner that forecasts Sidebar Creative's department-level capacity for Q2 2026, with hypothetical project modeling and pipeline risk toggles.

**Architecture:** Single self-contained HTML file with split-view layout. Top half shows department capacity bars across 3 months (Apr/May/Jun). Bottom half shows the project list driving demand. Right sidebar has pipeline risk toggles. All data embedded as JS objects, reusing the same team/project/allocation structure from the resource allocator.

**Tech Stack:** HTML, CSS, vanilla JavaScript — zero external dependencies.

**Spec:** `docs/superpowers/specs/2026-04-07-capacity-planner-design.md`

**Data sources:**
- `brand/brand_definition.md` — Team roster, client portfolio, project timelines
- `data/client_sow_terms.md` — SOW hour allocations by discipline
- `tools/resource_allocator.html` — Reference for data structure and visual conventions

---

### Task 1: HTML Scaffold with Header, Stats, and Layout Shell

**Files:**
- Create: `tools/capacity_planner.html`

Build the page shell with header, stat cards, and the three-panel layout (top forecast, bottom projects, right sidebar).

- [ ] **Step 1: Create the HTML file with full document structure**

Create `tools/capacity_planner.html`. Include the complete CSS and HTML for the shell. The CSS should cover:

```css
/* Reset & Base — match existing tools */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { font-size: 14px; }
body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f5f5f5; color: #222; min-width: 1200px;
}

/* Header */
.header { background: #1a1a1a; color: #fff; padding: 14px 28px; display: flex; align-items: center; justify-content: space-between; }
.header-left { display: flex; align-items: baseline; gap: 18px; }
.header-brand { font-size: 1.25rem; font-weight: 700; letter-spacing: .02em; }
.header-title { font-size: .95rem; font-weight: 400; color: #aaa; }
.header-date { font-size: .85rem; color: #777; }

/* Stats Row */
.stats-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 14px; padding: 18px 28px; }
.stat-card { background: #fff; border-radius: 6px; padding: 16px 20px; box-shadow: 0 1px 3px rgba(0,0,0,.08); }
.stat-label { font-size: .75rem; text-transform: uppercase; letter-spacing: .06em; color: #777; margin-bottom: 4px; }
.stat-value { font-size: 1.6rem; font-weight: 700; }
.stat-value.red { color: #dc3545; }
.stat-value.green { color: #28a745; }
.stat-value.blue { color: #007bff; }

/* Main Layout — split view with sidebar */
.main-layout { display: flex; gap: 14px; padding: 0 28px 28px; }
.main-content { flex: 3; min-width: 0; }
.sidebar { flex: 0 0 260px; }

/* Panel */
.panel { background: #fff; border-radius: 6px; box-shadow: 0 1px 3px rgba(0,0,0,.08); margin-bottom: 14px; }
.panel-header { background: #1a1a1a; color: #fff; padding: 10px 16px; font-size: .85rem; font-weight: 600; border-radius: 6px 6px 0 0; }
.panel-body { padding: 16px; }
```

The HTML body should contain:

```html
<div class="header">
  <div class="header-left">
    <div class="header-brand">Sidebar Creative</div>
    <div class="header-title">Capacity Planner</div>
  </div>
  <div class="header-date" id="headerDate"></div>
</div>

<div class="stats-row">
  <div class="stat-card">
    <div class="stat-label">Bottlenecks (&gt;100%)</div>
    <div class="stat-value red" id="statBottlenecks">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Bench Openings (&lt;70%)</div>
    <div class="stat-value green" id="statBench">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Hypothetical Projects</div>
    <div class="stat-value blue" id="statHypothetical">0</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Net Capacity Change</div>
    <div class="stat-value" id="statNetChange">0 hrs</div>
  </div>
</div>

<div class="main-layout">
  <div class="main-content">
    <div class="panel">
      <div class="panel-header">Capacity Forecast — Q2 2026 (Apr / May / Jun)</div>
      <div class="panel-body" id="forecastPanel"></div>
    </div>
    <div class="panel">
      <div class="panel-header">Projects Driving Demand</div>
      <div class="panel-body" style="padding:0;" id="projectsPanel"></div>
    </div>
  </div>
  <div class="sidebar">
    <div class="panel">
      <div class="panel-header">Pipeline Risks</div>
      <div class="panel-body" id="risksPanel"></div>
    </div>
  </div>
</div>
```

- [ ] **Step 2: Open in browser and verify layout renders**

Run: `open tools/capacity_planner.html`

Expected: Dark header, 4 stat card placeholders, two empty white panels on the left (forecast + projects), one empty panel on the right (risks).

- [ ] **Step 3: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Add capacity planner scaffold with header, stats, split layout"
```

---

### Task 2: Embed Data Layer

**Files:**
- Modify: `tools/capacity_planner.html`

Add the `<script>` block with team roster, projects (with timeline data), allocation matrix, and utility functions. Data structure mirrors the resource allocator but adds `endMonth` for project-based engagements.

- [ ] **Step 1: Add the team roster, projects, and allocation data**

Add a `<script>` tag before `</body>`. The data is identical to the resource allocator but with added timeline fields on projects.

```javascript
const MONTHS = ['apr', 'may', 'jun'];
const MONTH_LABELS = { apr: 'April', may: 'May', jun: 'June' };
const DEPTS = ['design', 'development', 'strategy', 'content', 'account'];
const DEPT_LABELS = { design: 'Design', development: 'Development', strategy: 'Strategy', content: 'Content', account: 'Account/PM' };
const WEEK_MONTH_FACTOR = 4.33;
```

**TEAM array:** Same 33 billable staff as resource allocator with id, name, initials, role, dept, capacity, billableTarget, currentLoad. Copy exactly from `tools/resource_allocator.html`.

**PROJECTS array:** Same 12 engagements but with added fields:
- `endMonth`: null for retainers (ongoing), or 'apr'/'may'/'jun'/'jul+' for project-based
- `endsHalf`: true if the project ends mid-month in its endMonth (half hours)

```javascript
const PROJECTS = [
  // Retainers — ongoing through Q2
  { id: 'evergreen', name: 'Evergreen Outdoor Co.', type: 'retainer', value: 45000, health: 'green', endMonth: null },
  { id: 'brightwash', name: 'Brightwash Home', type: 'retainer', value: 38000, health: 'green', endMonth: null },
  { id: 'vello', name: 'Vello Cycling', type: 'retainer', value: 32000, health: 'red', endMonth: null },
  { id: 'petal', name: 'Petal & Thorn', type: 'retainer', value: 28000, health: 'green', endMonth: null },
  { id: 'kova', name: 'Kova Skincare', type: 'retainer', value: 25000, health: 'yellow', endMonth: null },
  { id: 'drift', name: 'Drift Coffee Roasters', type: 'retainer', value: 22000, health: 'green', endMonth: null },
  { id: 'summit', name: 'Summit Pet Supply', type: 'retainer', value: 18000, health: 'green', endMonth: null },
  { id: 'moonridge', name: 'Moonridge Spirits', type: 'retainer', value: 12000, health: 'yellow', endMonth: null },
  // Project-based — with end dates
  { id: 'river_district', name: 'River District Rebrand', type: 'project', value: 85000, health: 'green', burned: 73, status: 'On track', endMonth: 'apr', endsHalf: true },
  { id: 'cascade', name: 'Cascade Athletic Website', type: 'project', value: 120000, health: 'green', burned: 40, status: 'On track', endMonth: 'jun', endsHalf: true },
  { id: 'fieldwork', name: 'Fieldwork Provisions Launch', type: 'project', value: 95000, health: 'green', burned: 19, status: 'Early stage', endMonth: 'jul+', endsHalf: false },
  { id: 'nw_natural', name: 'NW Natural Rebrand', type: 'project', value: 55000, health: 'yellow', burned: 75, status: 'Over scope', endMonth: 'apr', endsHalf: true },
];
```

**Allocations:** Same nested object as resource allocator. Copy the exact allocation assignments.

- [ ] **Step 2: Add forecast calculation functions**

```javascript
// Get the multiplier for a project in a given month
// Returns 1 (full hours), 0.5 (ending mid-month), or 0 (project ended)
function getProjectMonthMultiplier(project, month) {
  if (!project.endMonth || project.endMonth === 'jul+') return 1;
  const monthIndex = MONTHS.indexOf(month);
  const endIndex = MONTHS.indexOf(project.endMonth);
  if (endIndex === -1) return 1; // endMonth not in our quarter
  if (monthIndex < endIndex) return 1;
  if (monthIndex === endIndex) return project.endsHalf ? 0.5 : 1;
  return 0; // past end month
}

// Get monthly hours for a project by department
function getProjectDeptMonthHours(projectId, dept, month) {
  const project = PROJECTS.find(p => p.id === projectId);
  const mult = getProjectMonthMultiplier(project, month);
  if (mult === 0) return 0;
  let total = 0;
  const projAllocs = allocations[projectId] || {};
  for (const personId in projAllocs) {
    const person = TEAM.find(t => t.id === personId);
    if (person && person.dept === dept) {
      total += projAllocs[personId] * WEEK_MONTH_FACTOR;
    }
  }
  return Math.round(total * mult);
}

// Get total demand for a department in a month (all projects + hypotheticals)
function getDeptMonthDemand(dept, month) {
  let total = 0;
  PROJECTS.forEach(p => {
    total += getProjectDeptMonthHours(p.id, dept, month);
  });
  // Add risk adjustments
  total += getRiskAdjustment(dept, month);
  // Add hypothetical projects
  hypotheticalProjects.forEach(hp => {
    total += (hp.hours[month] && hp.hours[month][dept]) || 0;
  });
  return Math.round(total);
}

// Get department monthly capacity (sum of billableTarget * 4.33 for all staff in dept)
function getDeptCapacity(dept) {
  return Math.round(TEAM.filter(t => t.dept === dept).reduce((sum, t) => sum + t.billableTarget * WEEK_MONTH_FACTOR, 0));
}

// Get capacity bar color based on utilization percentage
function getCapacityColor(pct) {
  if (pct > 100) return '#dc3545';
  if (pct >= 90) return '#ffc107';
  if (pct >= 70) return '#555';
  return '#28a745';
}

function getCapacityLabel(pct) {
  if (pct > 100) return 'Bottleneck';
  if (pct >= 90) return 'Tight';
  if (pct >= 70) return 'Normal';
  return 'Bench time';
}

function formatMoney(n) {
  return n >= 1000 ? '$' + Math.round(n / 1000) + 'K' : '$' + n;
}

// State
let hypotheticalProjects = [];
let riskToggles = { velloChurns: false, kovaDownsizes: false, summitExpands: false };
let expandedDept = null;
```

- [ ] **Step 3: Add risk adjustment function**

```javascript
function getRiskAdjustment(dept, month) {
  let adjustment = 0;

  // Vello Churns — remove allocations from May and June
  if (riskToggles.velloChurns && (month === 'may' || month === 'jun')) {
    const velloAllocs = allocations['vello'] || {};
    for (const personId in velloAllocs) {
      const person = TEAM.find(t => t.id === personId);
      if (person && person.dept === dept) {
        adjustment -= velloAllocs[personId] * WEEK_MONTH_FACTOR;
      }
    }
  }

  // Kova Downsizes — reduce by 30% in June
  if (riskToggles.kovaDownsizes && month === 'jun') {
    const kovaAllocs = allocations['kova'] || {};
    for (const personId in kovaAllocs) {
      const person = TEAM.find(t => t.id === personId);
      if (person && person.dept === dept) {
        adjustment -= Math.round(kovaAllocs[personId] * WEEK_MONTH_FACTOR * 0.3);
      }
    }
  }

  // Summit Expands — add 55% from May onward
  if (riskToggles.summitExpands && (month === 'may' || month === 'jun')) {
    const summitAllocs = allocations['summit'] || {};
    for (const personId in summitAllocs) {
      const person = TEAM.find(t => t.id === personId);
      if (person && person.dept === dept) {
        adjustment += Math.round(summitAllocs[personId] * WEEK_MONTH_FACTOR * 0.55);
      }
    }
  }

  return Math.round(adjustment);
}
```

- [ ] **Step 4: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Add data layer with team, projects, allocations, and forecast functions"
```

---

### Task 3: Render Department Capacity Forecast (Top Half)

**Files:**
- Modify: `tools/capacity_planner.html`

Build the `renderForecast()` function that populates the top panel with department capacity bars.

- [ ] **Step 1: Add forecast CSS**

Add to the style block:

```css
/* Forecast */
.dept-row { margin-bottom: 16px; cursor: pointer; }
.dept-row:last-child { margin-bottom: 0; }
.dept-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 6px; }
.dept-name { font-weight: 700; font-size: 13px; }
.dept-target { font-size: 11px; color: #888; }
.dept-bars { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; }
.bar-month { font-size: 10px; color: #888; margin-bottom: 3px; text-transform: uppercase; }
.bar-track { background: #eee; border-radius: 4px; height: 28px; position: relative; overflow: hidden; }
.bar-fill { position: absolute; left: 0; top: 0; bottom: 0; border-radius: 4px; transition: width .3s; }
.bar-label { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 600; }
.bar-status { font-size: 10px; font-weight: 600; margin-top: 2px; }
.dept-expand { margin-top: 10px; padding-top: 10px; border-top: 1px solid #eee; }
.person-row { display: grid; grid-template-columns: 200px 1fr 1fr 1fr; gap: 10px; align-items: center; padding: 4px 0; font-size: 12px; }
.person-name { font-weight: 600; }
.person-role { font-size: 10px; color: #888; }
.person-bar { height: 18px; }
/* Hiring Signals */
.signals { margin-top: 16px; padding-top: 12px; border-top: 1px solid #eee; }
.signal { font-size: 11px; margin-bottom: 4px; }
.signal.red { color: #dc3545; }
.signal.green { color: #28a745; }
.signals-title { font-size: 11px; font-weight: 600; color: #555; margin-bottom: 6px; text-transform: uppercase; }
```

- [ ] **Step 2: Write renderForecast()**

```javascript
function renderForecast() {
  let html = '';

  DEPTS.forEach(dept => {
    const capacity = getDeptCapacity(dept);
    const staffCount = TEAM.filter(t => t.dept === dept).length;
    const label = DEPT_LABELS[dept];

    html += `<div class="dept-row" onclick="toggleExpand('${dept}')">`;
    html += `<div class="dept-header"><div class="dept-name">${label} (${staffCount} staff)</div><div class="dept-target">Target: ${capacity} hrs/mo</div></div>`;
    html += '<div class="dept-bars">';

    MONTHS.forEach(month => {
      const demand = getDeptMonthDemand(dept, month);
      const pct = Math.round(demand / capacity * 100);
      const color = getCapacityColor(pct);
      const statusLabel = getCapacityLabel(pct);
      const fillWidth = Math.min(100, pct);

      html += `<div>`;
      html += `<div class="bar-month">${MONTH_LABELS[month]}</div>`;
      html += `<div class="bar-track"><div class="bar-fill" style="width:${fillWidth}%;background:${color};"></div>`;
      html += `<div class="bar-label"><span style="color:${color};">${demand}</span><span style="color:#888;margin:0 2px;">/</span><span style="color:#555;">${capacity}</span></div></div>`;
      html += `<div class="bar-status" style="color:${color};">${pct}% — ${statusLabel}</div>`;
      html += `</div>`;
    });

    html += '</div>';

    // Expanded person rows
    if (expandedDept === dept) {
      html += '<div class="dept-expand">';
      const deptTeam = TEAM.filter(t => t.dept === dept);
      deptTeam.forEach(person => {
        html += `<div class="person-row">`;
        html += `<div><div class="person-name">${person.name}</div><div class="person-role">${person.role}</div></div>`;
        MONTHS.forEach(month => {
          const personDemand = getPersonMonthDemand(person.id, month);
          const personCap = Math.round(person.billableTarget * WEEK_MONTH_FACTOR);
          const pPct = Math.round(personDemand / personCap * 100);
          const pColor = getCapacityColor(pPct);
          const pFill = Math.min(100, pPct);
          html += `<div><div class="bar-track person-bar"><div class="bar-fill" style="width:${pFill}%;background:${pColor};"></div><div class="bar-label" style="font-size:10px;"><span style="color:${pColor};">${personDemand}</span><span style="color:#888;margin:0 2px;">/</span><span>${personCap}</span></div></div></div>`;
        });
        html += '</div>';
      });
      html += '</div>';
    }

    html += '</div>';
  });

  // Hiring signals
  html += '<div class="signals"><div class="signals-title">Hiring Signals</div>';
  DEPTS.forEach(dept => {
    const capacity = getDeptCapacity(dept);
    const label = DEPT_LABELS[dept];
    MONTHS.forEach(month => {
      const demand = getDeptMonthDemand(dept, month);
      const pct = Math.round(demand / capacity * 100);
      if (pct > 100) {
        html += `<div class="signal red">${label} bottleneck in ${MONTH_LABELS[month]} — ${demand - capacity} hrs over capacity</div>`;
      }
      if (pct < 60) {
        html += `<div class="signal green">${label} bench opens in ${MONTH_LABELS[month]} — ${capacity - demand} hrs available</div>`;
      }
    });
  });
  html += '</div>';

  document.getElementById('forecastPanel').innerHTML = html;
}

function getPersonMonthDemand(personId, month) {
  let total = 0;
  PROJECTS.forEach(proj => {
    const mult = getProjectMonthMultiplier(proj, month);
    const hrs = (allocations[proj.id] && allocations[proj.id][personId]) || 0;
    total += hrs * WEEK_MONTH_FACTOR * mult;
  });
  // Risk adjustments for this person
  total += getPersonRiskAdjustment(personId, month);
  // Hypothetical projects don't assign to individuals
  return Math.round(total);
}

function getPersonRiskAdjustment(personId, month) {
  let adj = 0;
  if (riskToggles.velloChurns && (month === 'may' || month === 'jun')) {
    adj -= ((allocations['vello'] && allocations['vello'][personId]) || 0) * WEEK_MONTH_FACTOR;
  }
  if (riskToggles.kovaDownsizes && month === 'jun') {
    adj -= Math.round(((allocations['kova'] && allocations['kova'][personId]) || 0) * WEEK_MONTH_FACTOR * 0.3);
  }
  if (riskToggles.summitExpands && (month === 'may' || month === 'jun')) {
    adj += Math.round(((allocations['summit'] && allocations['summit'][personId]) || 0) * WEEK_MONTH_FACTOR * 0.55);
  }
  return adj;
}

function toggleExpand(dept) {
  expandedDept = expandedDept === dept ? null : dept;
  renderForecast();
}
```

- [ ] **Step 3: Add initial render call**

```javascript
document.getElementById('headerDate').textContent = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });

function renderAll() {
  renderForecast();
  renderProjects();
  renderRisks();
  updateStats();
}

renderAll();
```

Note: `renderProjects()`, `renderRisks()`, and `updateStats()` will be implemented in subsequent tasks. For now, add them as empty stub functions so the page doesn't error:

```javascript
function renderProjects() {}
function renderRisks() {}
function updateStats() {}
```

- [ ] **Step 4: Verify the forecast renders**

Run: `open tools/capacity_planner.html`

Expected: 5 department rows with capacity bars across 3 months. April should show bottlenecks in some departments (all current projects still active). May/June should show decreased demand as projects end. Hiring signals should appear below. Clicking a department should expand to show individual people.

- [ ] **Step 5: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Render department capacity forecast with expandable rows and hiring signals"
```

---

### Task 4: Render Project List (Bottom Half)

**Files:**
- Modify: `tools/capacity_planner.html`

Build `renderProjects()` that shows all 12 engagements with monthly hours and timeline indicators.

- [ ] **Step 1: Add project list CSS**

```css
/* Project List */
.project-table { width: 100%; border-collapse: collapse; font-size: 12px; }
.project-table thead th { padding: 8px 12px; text-align: left; background: #f5f5f5; border-bottom: 1px solid #ddd; font-weight: 600; }
.project-table thead th.month-col { text-align: center; width: 100px; }
.project-table tbody td { padding: 6px 12px; border-bottom: 1px solid #eee; }
.project-table tbody td.month-col { text-align: center; font-weight: 600; }
.project-table .section-label td { background: #f0f0f0; font-weight: 700; font-size: 11px; text-transform: uppercase; letter-spacing: 1px; color: #555; padding: 6px 12px; }
.timeline-tag { font-size: 10px; margin-left: 6px; }
.hours-zero { color: #ccc; font-weight: 400; }
.hours-risk-removed { text-decoration: line-through; color: #dc3545; opacity: 0.6; }
.hours-risk-added { color: #28a745; font-weight: 700; }
/* Hypothetical row */
.hypo-row { border: 2px dashed #007bff; background: #007bff08; }
.hypo-row td { border-bottom: none; }
.add-project-btn { color: #007bff; font-weight: 600; cursor: pointer; border: 2px dashed #007bff; background: #007bff08; padding: 8px 12px; text-align: center; border-radius: 4px; margin: 8px; }
.add-project-btn:hover { background: #007bff15; }
.remove-hypo { color: #dc3545; cursor: pointer; font-size: 14px; margin-right: 6px; }
.remove-hypo:hover { color: #a71d2a; }
.hypo-name-input { border: 1px solid #007bff; border-radius: 3px; padding: 2px 6px; font-size: 12px; font-family: inherit; width: 140px; }
```

- [ ] **Step 2: Write renderProjects()**

Replace the stub `renderProjects` with:

```javascript
function renderProjects() {
  let html = '<table class="project-table"><thead><tr>';
  html += '<th>Project</th>';
  MONTHS.forEach(m => { html += `<th class="month-col">${MONTH_LABELS[m]}</th>`; });
  html += '</tr></thead><tbody>';

  // Retainers
  html += '<tr class="section-label"><td colspan="4">Retainer Clients</td></tr>';
  PROJECTS.filter(p => p.type === 'retainer').forEach(proj => {
    html += renderProjectRow(proj);
  });

  // Project-based
  html += '<tr class="section-label"><td colspan="4">Project-Based</td></tr>';
  PROJECTS.filter(p => p.type === 'project').forEach(proj => {
    html += renderProjectRow(proj);
  });

  // Hypothetical projects
  if (hypotheticalProjects.length > 0) {
    html += '<tr class="section-label"><td colspan="4">Hypothetical</td></tr>';
    hypotheticalProjects.forEach((hp, idx) => {
      html += renderHypotheticalRow(hp, idx);
    });
  }

  html += '</tbody></table>';
  html += '<div class="add-project-btn" onclick="addHypotheticalProject()">+ New Project</div>';

  document.getElementById('projectsPanel').innerHTML = html;
}

function renderProjectRow(proj) {
  const timelineTag = getTimelineTag(proj);
  const valueLabel = proj.type === 'retainer' ? formatMoney(proj.value) + '/mo' : formatMoney(proj.value) + ' budget';
  let html = '<tr>';
  html += `<td><span style="font-weight:600;">${proj.name}</span> <span style="color:#888;font-size:10px;">${valueLabel}</span>${timelineTag}</td>`;
  MONTHS.forEach(month => {
    let totalHrs = getProjectMonthTotal(proj.id, month);
    const isRiskAffected = isProjectRiskAffected(proj.id, month);
    if (totalHrs === 0) {
      html += '<td class="month-col"><span class="hours-zero">—</span></td>';
    } else if (isRiskAffected === 'removed') {
      html += `<td class="month-col"><span class="hours-risk-removed">${totalHrs}</span></td>`;
    } else if (isRiskAffected === 'increased') {
      const base = getProjectMonthTotal(proj.id, month, true);
      html += `<td class="month-col"><span class="hours-risk-added">${totalHrs}</span> <span style="font-size:10px;color:#888;">(was ${base})</span></td>`;
    } else {
      html += `<td class="month-col">${totalHrs}</td>`;
    }
  });
  html += '</tr>';
  return html;
}

function getProjectMonthTotal(projectId, month, ignoreRisks) {
  const project = PROJECTS.find(p => p.id === projectId);
  const mult = getProjectMonthMultiplier(project, month);
  if (mult === 0) return 0;
  let total = 0;
  const projAllocs = allocations[projectId] || {};
  for (const personId in projAllocs) {
    total += projAllocs[personId] * WEEK_MONTH_FACTOR;
  }
  total = Math.round(total * mult);
  if (!ignoreRisks) {
    // Apply risk adjustments at project level
    total += getProjectRiskAdjustment(projectId, month);
  }
  return Math.max(0, Math.round(total));
}

function getProjectRiskAdjustment(projectId, month) {
  let adj = 0;
  if (projectId === 'vello' && riskToggles.velloChurns && (month === 'may' || month === 'jun')) {
    // Remove all hours
    const projAllocs = allocations['vello'] || {};
    for (const pid in projAllocs) { adj -= projAllocs[pid] * WEEK_MONTH_FACTOR; }
  }
  if (projectId === 'kova' && riskToggles.kovaDownsizes && month === 'jun') {
    const projAllocs = allocations['kova'] || {};
    for (const pid in projAllocs) { adj -= Math.round(projAllocs[pid] * WEEK_MONTH_FACTOR * 0.3); }
  }
  if (projectId === 'summit' && riskToggles.summitExpands && (month === 'may' || month === 'jun')) {
    const projAllocs = allocations['summit'] || {};
    for (const pid in projAllocs) { adj += Math.round(projAllocs[pid] * WEEK_MONTH_FACTOR * 0.55); }
  }
  return Math.round(adj);
}

function isProjectRiskAffected(projectId, month) {
  if (projectId === 'vello' && riskToggles.velloChurns && (month === 'may' || month === 'jun')) return 'removed';
  if (projectId === 'kova' && riskToggles.kovaDownsizes && month === 'jun') return 'removed';
  if (projectId === 'summit' && riskToggles.summitExpands && (month === 'may' || month === 'jun')) return 'increased';
  return false;
}

function getTimelineTag(proj) {
  if (proj.type === 'retainer') return '<span class="timeline-tag" style="color:#888;">Ongoing</span>';
  if (proj.endMonth === 'apr') return '<span class="timeline-tag" style="color:#dc3545;">Ends Apr</span>';
  if (proj.endMonth === 'may') return '<span class="timeline-tag" style="color:#ffc107;">Ends May</span>';
  if (proj.endMonth === 'jun') return '<span class="timeline-tag" style="color:#28a745;">Ends Jun</span>';
  return '<span class="timeline-tag" style="color:#888;">Through Jul+</span>';
}
```

- [ ] **Step 3: Verify project list renders**

Open the page. Expected: All 12 projects listed with monthly hour totals. River District and NW Natural show hours only in April (half). Cascade shows full Apr/May, half June. Fieldwork shows full hours in all 3 months. Retainers show consistent hours across all months.

- [ ] **Step 4: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Render project list with monthly hours and timeline indicators"
```

---

### Task 5: Pipeline Risk Toggles (Right Sidebar)

**Files:**
- Modify: `tools/capacity_planner.html`

Build `renderRisks()` with styled toggle switches that recalculate the forecast.

- [ ] **Step 1: Add toggle CSS**

```css
/* Toggles */
.risk-toggle { margin-bottom: 14px; }
.toggle-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 4px; }
.toggle-name { font-size: 12px; font-weight: 600; }
.toggle-switch { width: 36px; height: 20px; border-radius: 10px; background: #ccc; position: relative; cursor: pointer; transition: background .2s; }
.toggle-switch.on { background: #dc3545; }
.toggle-switch.on-green { background: #28a745; }
.toggle-knob { width: 16px; height: 16px; background: #fff; border-radius: 50%; position: absolute; top: 2px; left: 2px; box-shadow: 0 1px 2px rgba(0,0,0,.2); transition: left .2s; }
.toggle-switch.on .toggle-knob, .toggle-switch.on-green .toggle-knob { left: 18px; }
.toggle-desc { font-size: 10px; color: #888; }
.risk-divider { border-top: 1px solid #eee; padding-top: 12px; margin-top: 12px; }
```

- [ ] **Step 2: Write renderRisks()**

Replace the stub:

```javascript
function renderRisks() {
  let html = '';

  // Vello Churns
  const velloOn = riskToggles.velloChurns;
  html += `<div class="risk-toggle">`;
  html += `<div class="toggle-header"><span class="toggle-name">Vello Churns</span>`;
  html += `<div class="toggle-switch ${velloOn ? 'on' : ''}" onclick="toggleRisk('velloChurns')"><div class="toggle-knob"></div></div>`;
  html += `</div><div class="toggle-desc">Removes Vello allocations from May &amp; June (~77 hrs/mo freed)</div></div>`;

  // Kova Downsizes
  const kovaOn = riskToggles.kovaDownsizes;
  html += `<div class="risk-toggle">`;
  html += `<div class="toggle-header"><span class="toggle-name">Kova Downsizes</span>`;
  html += `<div class="toggle-switch ${kovaOn ? 'on' : ''}" onclick="toggleRisk('kovaDownsizes')"><div class="toggle-knob"></div></div>`;
  html += `</div><div class="toggle-desc">Reduces Kova by 30% in June (~44 hrs/mo saved)</div></div>`;

  // Summit Expands
  const summitOn = riskToggles.summitExpands;
  html += `<div class="risk-toggle">`;
  html += `<div class="toggle-header"><span class="toggle-name">Summit Expands</span>`;
  html += `<div class="toggle-switch ${summitOn ? 'on-green' : ''}" onclick="toggleRisk('summitExpands')"><div class="toggle-knob"></div></div>`;
  html += `</div><div class="toggle-desc">Adds ~55% to Summit from May ($18K → $28K, ~62 hrs/mo added)</div></div>`;

  // Hiring Signals (duplicated in sidebar for visibility)
  html += '<div class="risk-divider"><div class="signals-title">Hiring Signals</div>';
  DEPTS.forEach(dept => {
    const capacity = getDeptCapacity(dept);
    const label = DEPT_LABELS[dept];
    MONTHS.forEach(month => {
      const demand = getDeptMonthDemand(dept, month);
      const pct = Math.round(demand / capacity * 100);
      if (pct > 100) {
        html += `<div class="signal red">${label} bottleneck in ${MONTH_LABELS[month]}</div>`;
      }
      if (pct < 60) {
        html += `<div class="signal green">${label} bench in ${MONTH_LABELS[month]}</div>`;
      }
    });
  });
  html += '</div>';

  document.getElementById('risksPanel').innerHTML = html;
}

function toggleRisk(riskKey) {
  riskToggles[riskKey] = !riskToggles[riskKey];
  renderAll();
}
```

- [ ] **Step 3: Write updateStats()**

Replace the stub:

```javascript
function updateStats() {
  let bottlenecks = 0, bench = 0, netChange = 0;

  DEPTS.forEach(dept => {
    const capacity = getDeptCapacity(dept);
    MONTHS.forEach(month => {
      const demand = getDeptMonthDemand(dept, month);
      const pct = Math.round(demand / capacity * 100);
      if (pct > 100) bottlenecks++;
      if (pct < 70) bench++;
    });
  });

  // Net change from active risk toggles
  DEPTS.forEach(dept => {
    MONTHS.forEach(month => {
      netChange += getRiskAdjustment(dept, month);
    });
  });

  document.getElementById('statBottlenecks').textContent = bottlenecks;
  document.getElementById('statBench').textContent = bench;
  document.getElementById('statHypothetical').textContent = hypotheticalProjects.length;
  const sign = netChange >= 0 ? '+' : '';
  document.getElementById('statNetChange').textContent = sign + netChange + ' hrs';
  document.getElementById('statNetChange').style.color = netChange > 0 ? '#dc3545' : (netChange < 0 ? '#28a745' : '#555');
}
```

- [ ] **Step 4: Verify toggles work**

Open the page, click the Vello Churns toggle. Expected: Toggle turns red, Vello row in the project list shows strikethrough hours in May/June, capacity bars for May/June decrease, hiring signals update. Toggle it off — everything reverts.

- [ ] **Step 5: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Add pipeline risk toggles with real-time forecast recalculation"
```

---

### Task 6: Hypothetical Project Feature

**Files:**
- Modify: `tools/capacity_planner.html`

Add the "New Project" button and editable hypothetical project rows with department-level hour entry popovers.

- [ ] **Step 1: Add hypothetical project CSS**

```css
/* Hypothetical popover */
.hypo-popover {
  position: absolute; top: 100%; left: 50%; transform: translateX(-50%);
  background: #fff; border: 2px solid #007bff; border-radius: 6px;
  padding: 10px 14px; box-shadow: 0 4px 12px rgba(0,0,0,.15);
  z-index: 10; min-width: 200px; font-size: 12px;
}
.hypo-popover label { display: flex; align-items: center; justify-content: space-between; margin-bottom: 6px; }
.hypo-popover label span { font-weight: 600; }
.hypo-popover input[type=number] {
  width: 50px; padding: 3px; text-align: center; font-size: 12px;
  font-family: inherit; border: 1px solid #ccc; border-radius: 3px;
}
.hypo-popover .popover-done {
  display: block; width: 100%; margin-top: 8px; padding: 4px; background: #007bff;
  color: #fff; border: none; border-radius: 4px; cursor: pointer; font-family: inherit; font-size: 12px;
}
.hypo-cell { position: relative; }
```

- [ ] **Step 2: Write hypothetical project functions**

```javascript
function addHypotheticalProject() {
  hypotheticalProjects.push({
    name: 'New Project',
    hours: { apr: {}, may: {}, jun: {} }
  });
  renderAll();
}

function removeHypotheticalProject(idx) {
  hypotheticalProjects.splice(idx, 1);
  renderAll();
}

function updateHypoName(idx, name) {
  hypotheticalProjects[idx].name = name;
}

function renderHypotheticalRow(hp, idx) {
  let html = '<tr class="hypo-row">';
  html += `<td><span class="remove-hypo" onclick="removeHypotheticalProject(${idx})">&times;</span>`;
  html += `<input class="hypo-name-input" value="${hp.name}" onchange="updateHypoName(${idx}, this.value)"></td>`;
  MONTHS.forEach(month => {
    const monthTotal = Object.values(hp.hours[month] || {}).reduce((s, v) => s + (v || 0), 0);
    html += `<td class="month-col hypo-cell" onclick="openHypoPopover(event, ${idx}, '${month}')">`;
    html += monthTotal > 0 ? `<span style="color:#007bff;font-weight:700;">${monthTotal}</span>` : '<span style="color:#007bff;cursor:pointer;">+ add</span>';
    html += '</td>';
  });
  html += '</tr>';
  return html;
}

function openHypoPopover(event, idx, month) {
  event.stopPropagation();
  const cell = event.currentTarget;
  // Remove any existing popovers
  document.querySelectorAll('.hypo-popover').forEach(p => p.remove());

  const hp = hypotheticalProjects[idx];
  const hours = hp.hours[month] || {};

  const popover = document.createElement('div');
  popover.className = 'hypo-popover';
  let popHtml = `<div style="font-weight:600;margin-bottom:8px;">Hours by Department — ${MONTH_LABELS[month]}</div>`;
  DEPTS.forEach(dept => {
    const val = hours[dept] || 0;
    popHtml += `<label><span>${DEPT_LABELS[dept]}</span><input type="number" min="0" max="500" value="${val}" data-dept="${dept}"></label>`;
  });
  popHtml += '<button class="popover-done">Done</button>';
  popover.innerHTML = popHtml;

  cell.appendChild(popover);

  // Position check
  requestAnimationFrame(() => {
    const rect = popover.getBoundingClientRect();
    if (rect.right > window.innerWidth) { popover.style.left = 'auto'; popover.style.right = '0'; popover.style.transform = 'none'; }
    if (rect.bottom > window.innerHeight) { popover.style.top = 'auto'; popover.style.bottom = '100%'; }
  });

  popover.querySelector('.popover-done').addEventListener('click', function(e) {
    e.stopPropagation();
    const inputs = popover.querySelectorAll('input[type=number]');
    hp.hours[month] = {};
    inputs.forEach(inp => {
      const val = parseInt(inp.value) || 0;
      if (val > 0) hp.hours[month][inp.dataset.dept] = val;
    });
    renderAll();
  });

  // Close on outside click
  setTimeout(() => {
    document.addEventListener('click', function closePopover(e) {
      if (!popover.contains(e.target)) { popover.remove(); document.removeEventListener('click', closePopover); }
    });
  }, 0);
}
```

- [ ] **Step 3: Verify hypothetical projects work**

Open the page, click "+ New Project". Expected: A new row appears with dashed blue border. Click a month cell — popover opens with 5 department inputs. Enter hours, click Done. The cell shows the total, and the capacity bars in the top half update. Click "x" to remove — everything recalculates.

- [ ] **Step 4: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Add hypothetical project feature with department hour entry popovers"
```

---

### Task 7: Polish and Final Verification

**Files:**
- Modify: `tools/capacity_planner.html`

Edge case handling, scroll behavior, responsive cleanup.

- [ ] **Step 1: Add scroll and resize handlers**

Add before `renderAll()`:

```javascript
// Close popovers on scroll
document.addEventListener('scroll', function() {
  document.querySelectorAll('.hypo-popover').forEach(p => p.remove());
}, true);

// Debounced resize
let resizeTimer;
window.addEventListener('resize', function() {
  clearTimeout(resizeTimer);
  resizeTimer = setTimeout(renderAll, 200);
});
```

- [ ] **Step 2: Verify end-to-end**

Walk through this checklist in the browser:

1. Header shows "Sidebar Creative — Capacity Planner" with today's date
2. Stat cards show correct bottleneck and bench counts
3. 5 department rows show capacity bars for Apr/May/Jun
4. April shows high utilization (all projects active), May/June show decrease as projects end
5. Click "Design" — expands to show 12 individual designers with personal bars
6. Click "Design" again — collapses
7. Project list shows all 12 engagements with correct monthly hours
8. River District and NW Natural show hours in April only (half), zero in May/June
9. Toggle "Vello Churns" ON — Vello row shows strikethrough in May/June, capacity bars decrease, signals update
10. Toggle OFF — reverts
11. Toggle "Summit Expands" ON — Summit row shows increased hours in May/June
12. Click "+ New Project" — hypothetical row appears
13. Click a month cell — popover opens with 5 dept inputs
14. Enter Design=40, Dev=30, click Done — capacity bars update, stat cards update
15. Click "x" on hypothetical row — removed, everything recalculates

- [ ] **Step 3: Commit**

```bash
git add tools/capacity_planner.html
git commit -m "Polish capacity planner: scroll handling, edge cases"
```

---

### Task 8: Process Log, Tutorial, and Final Commit

**Files:**
- Modify: `docs/process_log.md`
- Create: `tutorials/03_capacity_planner.md`

Document the tool per CLAUDE.md requirements.

- [ ] **Step 1: Prepend a log entry to docs/process_log.md**

Add at the top (after YAML header), newest first:

```markdown
## 2026-04-07 — Capacity Planner (Tool 3)

**Prompt:** Build a capacity planner for Sidebar Creative. Read the team roster, current project allocations, and upcoming project pipeline. Forecast team capacity for the next quarter — show where bottlenecks will form, which roles need hiring or contracting, and when bench time opens up. Let me add hypothetical new projects and see how they impact capacity. Output as an interactive HTML file.

**Built:** Interactive capacity planner with split-view layout — department capacity forecast on top, project list on bottom, pipeline risk toggles in sidebar. Supports hypothetical project modeling with department-level hour entry.

**Key Decisions:**
- Monthly granularity (not weekly) — matches how agencies budget and plan
- Split view — top answers "where are bottlenecks?", bottom answers "which projects cause them?"
- Risk toggles over scenario builder — independent toggles are more flexible and simpler
- Inline hypothetical rows over sidebar form — consistent with resource allocator interaction pattern
- Department-level forecasting (not individual) — the resource allocator handles person-level planning

**Files Created/Modified:**
- `tools/capacity_planner.html` (created)
- `docs/superpowers/specs/2026-04-07-capacity-planner-design.md` (created)
- `docs/superpowers/plans/2026-04-07-capacity-planner.md` (created)
- `docs/process_log.md` (modified)
- `tutorials/03_capacity_planner.md` (created)
```

- [ ] **Step 2: Create the tutorial**

Create `tutorials/03_capacity_planner.md` with YAML header and step-by-step guide covering:
- The exact prompts used during brainstorming
- Key decisions: monthly vs weekly, split view, risk toggles, hypothetical project interaction
- Data derivation: how project end dates drive the monthly forecast
- How risk toggles modify the allocations
- The final output file path: `tools/capacity_planner.html`

- [ ] **Step 3: Commit**

```bash
git add docs/process_log.md tutorials/03_capacity_planner.md
git commit -m "Add process log and tutorial for capacity planner"
```
