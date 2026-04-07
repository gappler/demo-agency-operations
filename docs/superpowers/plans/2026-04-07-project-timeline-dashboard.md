# Project Timeline Dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an interactive Gantt-style HTML dashboard showing all 12 Sidebar Creative engagements with click-to-expand detail panels for budget, milestones, team, and health data.

**Architecture:** Single self-contained HTML file with inlined CSS and vanilla JavaScript. Data hardcoded as JS objects sourced from brand definition. CSS Grid for Gantt layout, event listeners for interactivity.

**Tech Stack:** HTML5, CSS3 (Grid, custom properties), vanilla JavaScript (ES6+)

---

## File Structure

- **Create:** `tools/project_timeline.html` — the complete dashboard (single file, all CSS/JS inlined)

No other files. All data is embedded in the HTML file as JavaScript constants.

---

### Task 1: HTML Skeleton and Branded Header

**Files:**
- Create: `tools/project_timeline.html`

- [ ] **Step 1: Create the HTML file with document structure and branded header**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Project Timeline — Sidebar Creative</title>
  <style>
    /* --- Reset & Base --- */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: #f5f5f5;
      color: #1a1a1a;
      line-height: 1.5;
    }

    /* --- Branded Header --- */
    .header {
      background: #1a1a1a;
      color: #fff;
      padding: 16px 32px;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .header-left {
      display: flex;
      align-items: center;
      gap: 16px;
    }
    .header-logo {
      font-size: 18px;
      font-weight: 700;
      letter-spacing: -0.5px;
    }
    .header-logo span {
      color: #a0a0a0;
      font-weight: 400;
    }
    .header-title {
      font-size: 14px;
      color: #a0a0a0;
      border-left: 1px solid #444;
      padding-left: 16px;
    }
    .header-date {
      font-size: 13px;
      color: #a0a0a0;
    }

    /* --- Summary Stats --- */
    .summary {
      display: flex;
      gap: 16px;
      padding: 20px 32px;
      background: #fff;
      border-bottom: 1px solid #e0e0e0;
    }
    .stat-card {
      flex: 1;
      padding: 12px 16px;
      background: #fafafa;
      border: 1px solid #e8e8e8;
      border-radius: 6px;
    }
    .stat-label {
      font-size: 11px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      color: #888;
      margin-bottom: 4px;
    }
    .stat-value {
      font-size: 24px;
      font-weight: 700;
    }
    .stat-sub {
      font-size: 12px;
      color: #888;
      margin-top: 2px;
    }

    /* --- Filter Bar --- */
    .filters {
      padding: 12px 32px;
      background: #fff;
      border-bottom: 1px solid #e0e0e0;
      display: flex;
      gap: 8px;
    }
    .filter-btn {
      padding: 6px 14px;
      border: 1px solid #d0d0d0;
      border-radius: 4px;
      background: #fff;
      font-size: 13px;
      cursor: pointer;
      color: #555;
    }
    .filter-btn.active {
      background: #1a1a1a;
      color: #fff;
      border-color: #1a1a1a;
    }

    /* --- Gantt Container --- */
    .gantt-container {
      padding: 24px 32px;
    }
    .gantt-section-title {
      font-size: 12px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      color: #888;
      margin-bottom: 8px;
      margin-top: 20px;
    }
    .gantt-section-title:first-child {
      margin-top: 0;
    }

    /* --- Gantt Grid --- */
    .gantt {
      display: grid;
      grid-template-columns: 220px 1fr;
      border: 1px solid #e0e0e0;
      border-radius: 6px;
      overflow: hidden;
      background: #fff;
    }

    /* Month headers */
    .gantt-header-label {
      padding: 10px 12px;
      font-size: 12px;
      font-weight: 600;
      color: #888;
      background: #fafafa;
      border-bottom: 1px solid #e0e0e0;
      border-right: 1px solid #e0e0e0;
    }
    .gantt-header-months {
      display: grid;
      background: #fafafa;
      border-bottom: 1px solid #e0e0e0;
    }
    .gantt-month {
      padding: 10px 4px;
      font-size: 11px;
      text-align: center;
      color: #888;
      border-right: 1px solid #f0f0f0;
    }

    /* Rows */
    .gantt-row-label {
      padding: 10px 12px;
      font-size: 13px;
      display: flex;
      align-items: center;
      gap: 8px;
      border-bottom: 1px solid #f0f0f0;
      border-right: 1px solid #e0e0e0;
      cursor: pointer;
      transition: background 0.15s;
    }
    .gantt-row-label:hover {
      background: #f5f5f5;
    }
    .gantt-row-label.selected {
      background: #f0f4ff;
    }
    .gantt-row-timeline {
      position: relative;
      border-bottom: 1px solid #f0f0f0;
      cursor: pointer;
    }
    .gantt-row-timeline:hover {
      background: #fafafa;
    }

    /* Month grid lines */
    .month-gridlines {
      position: absolute;
      inset: 0;
      display: grid;
      pointer-events: none;
    }
    .month-gridline {
      border-right: 1px solid #f0f0f0;
    }

    /* Bars */
    .gantt-bar-container {
      position: absolute;
      top: 6px;
      bottom: 6px;
      display: flex;
      align-items: center;
    }
    .gantt-bar {
      height: 100%;
      border-radius: 4px;
      position: relative;
      min-width: 8px;
      width: 100%;
      overflow: hidden;
    }
    .gantt-bar.green { background: #d4edda; border: 1px solid #a3d9b1; }
    .gantt-bar.yellow { background: #fff3cd; border: 1px solid #f0d77c; }
    .gantt-bar.red { background: #f8d7da; border: 1px solid #f0a0a8; }
    .gantt-bar.on-track { background: #d4edda; border: 1px solid #a3d9b1; }
    .gantt-bar.over-scope { background: #fff3cd; border: 1px solid #f0d77c; }
    .gantt-bar.early-stage { background: #d6e9f8; border: 1px solid #a0c4e8; }

    .gantt-bar-fill {
      position: absolute;
      left: 0;
      top: 0;
      bottom: 0;
      border-radius: 3px 0 0 3px;
      opacity: 0.4;
    }
    .gantt-bar.green .gantt-bar-fill { background: #28a745; }
    .gantt-bar.yellow .gantt-bar-fill { background: #e6a817; }
    .gantt-bar.red .gantt-bar-fill { background: #dc3545; }
    .gantt-bar.on-track .gantt-bar-fill { background: #28a745; }
    .gantt-bar.over-scope .gantt-bar-fill { background: #e6a817; }
    .gantt-bar.early-stage .gantt-bar-fill { background: #3a8fd4; }

    /* Continuation arrows */
    .bar-arrow-left, .bar-arrow-right {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      font-size: 10px;
      color: #888;
    }
    .bar-arrow-left { left: -14px; }
    .bar-arrow-right { right: -14px; }

    /* Milestones */
    .milestone {
      position: absolute;
      top: 50%;
      transform: translate(-50%, -50%) rotate(45deg);
      width: 8px;
      height: 8px;
      background: #555;
      z-index: 2;
    }
    .milestone.completed { background: #28a745; }
    .milestone.upcoming { background: #888; }
    .milestone.overdue { background: #dc3545; }

    /* Renewal marker */
    .renewal-marker {
      position: absolute;
      top: 2px;
      bottom: 2px;
      width: 2px;
      background: #6c5ce7;
      z-index: 2;
    }
    .renewal-marker::after {
      content: 'R';
      position: absolute;
      top: -2px;
      left: 4px;
      font-size: 9px;
      color: #6c5ce7;
      font-weight: 700;
    }

    /* Today line */
    .today-line {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 2px;
      background: #dc3545;
      z-index: 3;
      pointer-events: none;
    }
    .today-line::after {
      content: 'Today';
      position: absolute;
      top: -18px;
      left: -16px;
      font-size: 9px;
      color: #dc3545;
      font-weight: 600;
      white-space: nowrap;
    }

    /* RAG dots */
    .rag-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      flex-shrink: 0;
    }
    .rag-dot.green { background: #28a745; }
    .rag-dot.yellow { background: #e6a817; }
    .rag-dot.red { background: #dc3545; }
    .rag-dot.on-track { background: #28a745; }
    .rag-dot.over-scope { background: #e6a817; }
    .rag-dot.early-stage { background: #3a8fd4; }

    /* Client name in row */
    .client-name {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* --- Tooltip --- */
    .tooltip {
      display: none;
      position: fixed;
      background: #1a1a1a;
      color: #fff;
      padding: 8px 12px;
      border-radius: 4px;
      font-size: 12px;
      max-width: 280px;
      z-index: 100;
      pointer-events: none;
      line-height: 1.4;
    }
    .tooltip.visible { display: block; }

    /* --- Detail Panel --- */
    .detail-panel {
      display: none;
      margin: 0 32px 24px;
      background: #fff;
      border: 1px solid #e0e0e0;
      border-radius: 6px;
      overflow: hidden;
    }
    .detail-panel.visible { display: block; }
    .detail-header {
      padding: 16px 20px;
      background: #fafafa;
      border-bottom: 1px solid #e0e0e0;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .detail-header-left {
      display: flex;
      align-items: center;
      gap: 12px;
    }
    .detail-client-name {
      font-size: 16px;
      font-weight: 600;
    }
    .detail-badge {
      padding: 3px 10px;
      border-radius: 12px;
      font-size: 11px;
      font-weight: 600;
      text-transform: uppercase;
    }
    .detail-badge.green { background: #d4edda; color: #1e7e34; }
    .detail-badge.yellow { background: #fff3cd; color: #856404; }
    .detail-badge.red { background: #f8d7da; color: #721c24; }
    .detail-badge.on-track { background: #d4edda; color: #1e7e34; }
    .detail-badge.over-scope { background: #fff3cd; color: #856404; }
    .detail-badge.early-stage { background: #d6e9f8; color: #1a5276; }
    .detail-close {
      background: none;
      border: none;
      font-size: 20px;
      cursor: pointer;
      color: #888;
      padding: 4px 8px;
    }
    .detail-body {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 0;
    }
    .detail-section {
      padding: 16px 20px;
      border-right: 1px solid #f0f0f0;
      border-bottom: 1px solid #f0f0f0;
    }
    .detail-section:nth-child(2n) { border-right: none; }
    .detail-section-title {
      font-size: 11px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      color: #888;
      margin-bottom: 10px;
      font-weight: 600;
    }

    /* Budget bar */
    .budget-bar {
      height: 8px;
      background: #e8e8e8;
      border-radius: 4px;
      overflow: hidden;
      margin: 8px 0;
    }
    .budget-bar-fill {
      height: 100%;
      border-radius: 4px;
      transition: width 0.3s;
    }
    .budget-bar-fill.healthy { background: #28a745; }
    .budget-bar-fill.warning { background: #e6a817; }
    .budget-bar-fill.danger { background: #dc3545; }
    .budget-row {
      display: flex;
      justify-content: space-between;
      font-size: 13px;
      margin-bottom: 4px;
    }
    .budget-row .label { color: #888; }
    .budget-row .value { font-weight: 600; }

    /* Health scores */
    .health-scores {
      display: flex;
      gap: 16px;
    }
    .health-score {
      text-align: center;
    }
    .health-score-value {
      font-size: 24px;
      font-weight: 700;
    }
    .health-score-label {
      font-size: 11px;
      color: #888;
    }
    .health-score-value.good { color: #28a745; }
    .health-score-value.ok { color: #e6a817; }
    .health-score-value.bad { color: #dc3545; }

    /* Milestones list */
    .milestone-list {
      list-style: none;
    }
    .milestone-item {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 4px 0;
      font-size: 13px;
    }
    .milestone-icon {
      width: 16px;
      height: 16px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 10px;
      flex-shrink: 0;
    }
    .milestone-icon.done { background: #d4edda; color: #1e7e34; }
    .milestone-icon.in-progress { background: #fff3cd; color: #856404; }
    .milestone-icon.upcoming { background: #e8e8e8; color: #888; }
    .milestone-icon.overdue { background: #f8d7da; color: #721c24; }
    .milestone-date {
      color: #888;
      font-size: 12px;
      margin-left: auto;
      flex-shrink: 0;
    }

    /* Team list */
    .team-list {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
    }
    .team-member {
      padding: 4px 10px;
      background: #f0f0f0;
      border-radius: 4px;
      font-size: 12px;
    }
    .team-member .role {
      color: #888;
    }

    /* Risk notes */
    .risk-note {
      padding: 8px 12px;
      background: #fff8f0;
      border-left: 3px solid #e6a817;
      font-size: 13px;
      margin-bottom: 6px;
      border-radius: 0 4px 4px 0;
    }
    .risk-note.critical {
      background: #fdf0f0;
      border-left-color: #dc3545;
    }
  </style>
</head>
<body>

  <!-- Header -->
  <div class="header">
    <div class="header-left">
      <div class="header-logo">Sidebar <span>Creative</span></div>
      <div class="header-title">Project Timeline Dashboard</div>
    </div>
    <div class="header-date" id="currentDate"></div>
  </div>

  <!-- Summary Stats -->
  <div class="summary" id="summaryStats"></div>

  <!-- Filters -->
  <div class="filters">
    <button class="filter-btn active" data-filter="all" onclick="setFilter('all')">All (12)</button>
    <button class="filter-btn" data-filter="retainers" onclick="setFilter('retainers')">Retainers (8)</button>
    <button class="filter-btn" data-filter="projects" onclick="setFilter('projects')">Projects (4)</button>
  </div>

  <!-- Gantt Timeline -->
  <div class="gantt-container" id="ganttContainer"></div>

  <!-- Detail Panel -->
  <div class="detail-panel" id="detailPanel"></div>

  <!-- Tooltip -->
  <div class="tooltip" id="tooltip"></div>

<script>
// ============================================================
// DATA
// ============================================================

const TODAY = new Date(2026, 3, 7); // April 7, 2026
const TIMELINE_START = new Date(2026, 0, 1); // Jan 2026
const TIMELINE_END = new Date(2026, 11, 31); // Dec 2026
const MONTHS = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
const TOTAL_MONTHS = 12;

const retainers = [
  {
    id: 'evergreen',
    name: 'Evergreen Outdoor Co.',
    type: 'retainer',
    industry: 'Outdoor / Lifestyle',
    retainer: 45000,
    sowStart: new Date(2024, 2, 1),
    sowEnd: new Date(2027, 1, 28),
    health: 'green',
    healthLabel: 'Green',
    allocatedHrs: 257,
    actualHrs: 248,
    utilization: 96,
    effectiveRate: 181,
    scores: { performance: 92, profitability: 85, relationship: 95, overall: 91 },
    team: [
      { name: 'Sophie Morales', role: 'AM' },
      { name: 'Nico Strand', role: 'Design Lead' },
      { name: 'Owen Marsh', role: 'Dev Lead' },
      { name: 'Celeste Durand', role: 'Strategy' }
    ],
    milestones: [
      { name: 'Q2 QBR', date: new Date(2026, 5, 30), status: 'upcoming' },
      { name: 'Holiday Campaign Planning', date: new Date(2026, 8, 1), status: 'upcoming' },
      { name: 'SOW Renewal', date: new Date(2027, 1, 28), status: 'upcoming' }
    ],
    risks: [],
    renewalDate: new Date(2027, 1, 28)
  },
  {
    id: 'brightwash',
    name: 'Brightwash Home',
    type: 'retainer',
    industry: 'Home / Cleaning',
    retainer: 38000,
    sowStart: new Date(2024, 5, 1),
    sowEnd: new Date(2027, 4, 31),
    health: 'green',
    healthLabel: 'Green',
    allocatedHrs: 224,
    actualHrs: 240,
    utilization: 107,
    effectiveRate: 158,
    scores: { performance: 80, profitability: 68, relationship: 88, overall: 78 },
    team: [
      { name: 'Tomás Reyes', role: 'AM' },
      { name: 'Yuki Tanaka', role: 'Design' },
      { name: 'Diana Kovač', role: 'Dev' },
      { name: 'Andre Kim', role: 'Strategy' }
    ],
    milestones: [
      { name: 'Subscription Model Optimization', date: new Date(2026, 4, 15), status: 'upcoming' },
      { name: 'Q3 QBR', date: new Date(2026, 8, 30), status: 'upcoming' },
      { name: 'SOW Renewal', date: new Date(2027, 4, 31), status: 'upcoming' }
    ],
    risks: ['Margin-thin due to untracked small changes — scope creep risk'],
    renewalDate: new Date(2027, 4, 31)
  },
  {
    id: 'vello',
    name: 'Vello Cycling',
    type: 'retainer',
    industry: 'Cycling / Fitness',
    retainer: 32000,
    sowStart: new Date(2025, 0, 1),
    sowEnd: new Date(2026, 5, 30), // month-to-month, estimated
    health: 'red',
    healthLabel: 'Red — Churn Risk',
    allocatedHrs: 183,
    actualHrs: 210,
    utilization: 115,
    effectiveRate: 152,
    scores: { performance: 45, profitability: 52, relationship: 35, overall: 44 },
    team: [
      { name: 'Sophie Morales', role: 'AM' },
      { name: 'Bea Cortez', role: 'Design' },
      { name: 'Leo Farias', role: 'Dev' },
      { name: 'Celeste Durand', role: 'Strategy' }
    ],
    milestones: [
      { name: 'Recovery Plan Checkpoint', date: new Date(2026, 3, 15), status: 'in-progress' },
      { name: 'Renewal Decision', date: new Date(2026, 5, 30), status: 'upcoming' }
    ],
    risks: [
      'New VP Marketing has different expectations — relationship strained',
      'Client requested competitive pitches from two agencies (Feb 2026)',
      'Two major deliverables missed deadlines in Jan–Feb 2026',
      'Operating month-to-month — 30-day termination notice'
    ],
    renewalDate: new Date(2026, 5, 30)
  },
  {
    id: 'petal',
    name: 'Petal & Thorn',
    type: 'retainer',
    industry: 'Floral / Gifting',
    retainer: 28000,
    sowStart: new Date(2024, 8, 1),
    sowEnd: new Date(2027, 7, 31),
    health: 'green',
    healthLabel: 'Green',
    allocatedHrs: 170,
    actualHrs: 162,
    utilization: 95,
    effectiveRate: 173,
    scores: { performance: 85, profitability: 90, relationship: 82, overall: 86 },
    team: [
      { name: 'Alina Park', role: 'AM' },
      { name: 'Finn Gallagher', role: 'Design' },
      { name: 'Hana Yoshida', role: 'Dev' },
      { name: 'Tessa Moreno', role: 'Strategy' }
    ],
    milestones: [
      { name: "Mother's Day Campaign", date: new Date(2026, 3, 20), status: 'in-progress' },
      { name: 'Summer Collection Launch', date: new Date(2026, 5, 15), status: 'upcoming' },
      { name: 'Holiday Campaign Prep', date: new Date(2026, 8, 15), status: 'upcoming' },
      { name: 'SOW Renewal', date: new Date(2027, 7, 31), status: 'upcoming' }
    ],
    risks: [],
    renewalDate: new Date(2027, 7, 31)
  },
  {
    id: 'kova',
    name: 'Kova Skincare',
    type: 'retainer',
    industry: 'Beauty / DTC',
    retainer: 25000,
    sowStart: new Date(2024, 10, 1),
    sowEnd: new Date(2026, 9, 31),
    health: 'yellow',
    healthLabel: 'Yellow — ROI Concerns',
    allocatedHrs: 147,
    actualHrs: 155,
    utilization: 105,
    effectiveRate: 161,
    scores: { performance: 55, profitability: 70, relationship: 72, overall: 64 },
    team: [
      { name: 'Tomás Reyes', role: 'AM' },
      { name: 'Jordan Tate', role: 'Design' },
      { name: 'Nadia Volkov', role: 'Dev' },
      { name: 'Andre Kim', role: 'Strategy' }
    ],
    milestones: [
      { name: 'Conversion Metrics Review', date: new Date(2026, 3, 30), status: 'upcoming' },
      { name: 'Quarterly Review — Possible Downsize', date: new Date(2026, 6, 1), status: 'upcoming' },
      { name: 'SOW Auto-Renewal', date: new Date(2026, 9, 31), status: 'upcoming' }
    ],
    risks: [
      'Web redesign (Feb 2026) has not improved conversion metrics',
      'Client questioning ROI — possible retainer downsize from $25K to $18K at Jul 2026 review'
    ],
    renewalDate: new Date(2026, 9, 31)
  },
  {
    id: 'drift',
    name: 'Drift Coffee Roasters',
    type: 'retainer',
    industry: 'Food & Beverage',
    retainer: 22000,
    sowStart: new Date(2024, 3, 1),
    sowEnd: new Date(2027, 2, 31),
    health: 'green',
    healthLabel: 'Green',
    allocatedHrs: 138,
    actualHrs: 132,
    utilization: 96,
    effectiveRate: 167,
    scores: { performance: 78, profitability: 88, relationship: 85, overall: 83 },
    team: [
      { name: 'Priya Nair', role: 'PM' },
      { name: 'Luna Esperanza', role: 'Design' },
      { name: 'Ravi Subramanian', role: 'Dev' },
      { name: 'Rowan Griffiths', role: 'Content' }
    ],
    milestones: [
      { name: 'Annual Brand Refresh', date: new Date(2026, 2, 31), status: 'done' },
      { name: 'Subscription UX Optimization', date: new Date(2026, 5, 30), status: 'upcoming' },
      { name: 'SOW Renewal', date: new Date(2027, 2, 31), status: 'upcoming' }
    ],
    risks: [],
    renewalDate: new Date(2027, 2, 31)
  },
  {
    id: 'summit',
    name: 'Summit Pet Supply',
    type: 'retainer',
    industry: 'Pet / E-commerce',
    retainer: 18000,
    sowStart: new Date(2025, 7, 1),
    sowEnd: new Date(2026, 6, 31),
    health: 'green',
    healthLabel: 'Green',
    allocatedHrs: 113,
    actualHrs: 108,
    utilization: 96,
    effectiveRate: 167,
    scores: { performance: 82, profitability: 80, relationship: 90, overall: 84 },
    team: [
      { name: 'James Whitfield', role: 'PM' },
      { name: 'Zara Osei', role: 'Design' },
      { name: 'Marcus Webb', role: 'Dev' },
      { name: 'Jia Liang', role: 'Content' }
    ],
    milestones: [
      { name: 'Scope Expansion Discussion', date: new Date(2026, 5, 15), status: 'upcoming' },
      { name: 'SOW Renewal / Upgrade', date: new Date(2026, 6, 31), status: 'upcoming' }
    ],
    risks: ['Client has asked about expanding scope twice — may outgrow $18K retainer'],
    renewalDate: new Date(2026, 6, 31)
  },
  {
    id: 'moonridge',
    name: 'Moonridge Spirits',
    type: 'retainer',
    industry: 'Craft Spirits',
    retainer: 12000,
    sowStart: new Date(2025, 11, 1),
    sowEnd: new Date(2026, 10, 30),
    health: 'yellow',
    healthLabel: 'Yellow — Over Scope',
    allocatedHrs: 77,
    actualHrs: 88,
    utilization: 114,
    effectiveRate: 136,
    scores: { performance: 65, profitability: 48, relationship: 70, overall: 60 },
    team: [
      { name: 'Alina Park', role: 'AM' },
      { name: 'Milo Varga', role: 'Design' },
      { name: 'Elodie Blanc', role: 'Dev' },
      { name: 'Elliot Saunders', role: 'Content' }
    ],
    milestones: [
      { name: 'Brand Identity Phase Complete', date: new Date(2026, 2, 31), status: 'done' },
      { name: 'Website Launch', date: new Date(2026, 5, 15), status: 'upcoming' },
      { name: 'SOW Review', date: new Date(2026, 10, 30), status: 'upcoming' }
    ],
    risks: [
      'Averaging 88 hrs/month vs. 77 hrs allocated — likely unprofitable',
      'Founding team indecisive — revision cycles 2x normal',
      'Revision limits (3 rounds) frequently exceeded'
    ],
    renewalDate: new Date(2026, 10, 30)
  }
];

const projects = [
  {
    id: 'river-district',
    name: 'River District Rebrand',
    type: 'project',
    client: 'River District BID',
    projectType: 'Brand identity + guidelines',
    budget: 85000,
    burned: 62000,
    burnPct: 73,
    start: new Date(2026, 0, 1),
    end: new Date(2026, 3, 30),
    health: 'on-track',
    healthLabel: 'On Track',
    team: [
      { name: 'Nico Strand', role: 'Art Director' },
      { name: 'Luna Esperanza', role: 'Brand Design' },
      { name: 'Lena Voss', role: 'Strategy Lead' }
    ],
    milestones: [
      { name: 'Discovery Complete', date: new Date(2026, 0, 31), status: 'done' },
      { name: 'Concepts Presented', date: new Date(2026, 1, 28), status: 'done' },
      { name: 'Refinement & Testing', date: new Date(2026, 2, 31), status: 'done' },
      { name: 'Guidelines Delivered', date: new Date(2026, 3, 30), status: 'in-progress' }
    ],
    risks: []
  },
  {
    id: 'cascade',
    name: 'Cascade Athletic Website',
    type: 'project',
    client: 'Cascade Athletic Wear',
    projectType: 'Shopify Plus build',
    budget: 120000,
    burned: 48000,
    burnPct: 40,
    start: new Date(2026, 1, 1),
    end: new Date(2026, 5, 30),
    health: 'on-track',
    healthLabel: 'On Track',
    team: [
      { name: 'Owen Marsh', role: 'Dev Lead' },
      { name: 'Diana Kovač', role: 'Senior Dev' },
      { name: 'Kai Bergström', role: 'UX Design' },
      { name: 'Yuki Tanaka', role: 'Visual Design' }
    ],
    milestones: [
      { name: 'UX/Design Phase', date: new Date(2026, 1, 28), status: 'done' },
      { name: 'Dev Sprint 1', date: new Date(2026, 2, 31), status: 'done' },
      { name: 'Dev Sprint 2', date: new Date(2026, 4, 15), status: 'upcoming' },
      { name: 'QA & Launch', date: new Date(2026, 5, 30), status: 'upcoming' }
    ],
    risks: []
  },
  {
    id: 'fieldwork',
    name: 'Fieldwork Provisions Launch',
    type: 'project',
    client: 'Fieldwork Provisions',
    projectType: 'Brand + web + launch campaign',
    budget: 95000,
    burned: 18000,
    burnPct: 19,
    start: new Date(2026, 2, 1),
    end: new Date(2026, 6, 31),
    health: 'early-stage',
    healthLabel: 'Early Stage',
    team: [
      { name: 'Bea Cortez', role: 'Design' },
      { name: 'Leo Farias', role: 'Dev' },
      { name: 'Celeste Durand', role: 'Strategy' },
      { name: 'Rowan Griffiths', role: 'Content' }
    ],
    milestones: [
      { name: 'Brand Strategy & Identity', date: new Date(2026, 3, 15), status: 'in-progress' },
      { name: 'Web Design', date: new Date(2026, 4, 15), status: 'upcoming' },
      { name: 'Web Development', date: new Date(2026, 5, 30), status: 'upcoming' },
      { name: 'Launch Campaign', date: new Date(2026, 6, 31), status: 'upcoming' }
    ],
    risks: []
  },
  {
    id: 'nw-natural',
    name: 'NW Natural Rebrand',
    type: 'project',
    client: 'NW Natural Foods Co-op',
    projectType: 'Visual identity refresh',
    budget: 55000,
    burned: 41000,
    burnPct: 75,
    start: new Date(2026, 1, 1),
    end: new Date(2026, 3, 30),
    health: 'over-scope',
    healthLabel: 'Over Scope',
    team: [
      { name: 'Nico Strand', role: 'Art Director' },
      { name: 'Finn Gallagher', role: 'Design' },
      { name: 'Tessa Moreno', role: 'Strategy' }
    ],
    milestones: [
      { name: 'Concept Exploration', date: new Date(2026, 1, 28), status: 'done' },
      { name: 'Refinement', date: new Date(2026, 2, 31), status: 'done' },
      { name: 'Final Delivery', date: new Date(2026, 3, 30), status: 'in-progress' }
    ],
    risks: ['75% budget burned with final delivery still in progress — over scope']
  }
];

const allEngagements = [...retainers, ...projects];

// ============================================================
// UTILITY FUNCTIONS
// ============================================================

function monthDiff(d1, d2) {
  return (d2.getFullYear() - d1.getFullYear()) * 12 + (d2.getMonth() - d1.getMonth());
}

function dateToTimelinePercent(date) {
  const totalDays = (TIMELINE_END - TIMELINE_START) / (1000 * 60 * 60 * 24);
  const dayOffset = (date - TIMELINE_START) / (1000 * 60 * 60 * 24);
  return Math.max(0, Math.min(100, (dayOffset / totalDays) * 100));
}

function formatCurrency(n) {
  return '$' + n.toLocaleString();
}

function formatDate(d) {
  return MONTHS[d.getMonth()] + ' ' + d.getDate() + ', ' + d.getFullYear();
}

function formatMonthYear(d) {
  return MONTHS[d.getMonth()] + ' ' + d.getFullYear();
}

function healthColor(score) {
  if (score >= 75) return 'good';
  if (score >= 50) return 'ok';
  return 'bad';
}

// ============================================================
// RENDER: CURRENT DATE
// ============================================================

document.getElementById('currentDate').textContent = formatDate(TODAY);

// ============================================================
// RENDER: SUMMARY STATS
// ============================================================

function renderSummary() {
  const atRisk = allEngagements.filter(e => e.health === 'red' || e.health === 'yellow' || e.health === 'over-scope');
  const onTrack = allEngagements.filter(e => e.health === 'green' || e.health === 'on-track' || e.health === 'early-stage');

  // Find next deadline
  const upcomingMilestones = allEngagements.flatMap(e =>
    (e.milestones || []).filter(m => m.status !== 'done' && m.date >= TODAY)
      .map(m => ({ ...m, client: e.name }))
  ).sort((a, b) => a.date - b.date);
  const nextDeadline = upcomingMilestones[0];

  document.getElementById('summaryStats').innerHTML = `
    <div class="stat-card">
      <div class="stat-label">Active Engagements</div>
      <div class="stat-value">${allEngagements.length}</div>
      <div class="stat-sub">8 retainers · 4 projects</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">At Risk</div>
      <div class="stat-value" style="color: ${atRisk.length > 0 ? '#dc3545' : '#28a745'}">${atRisk.length}</div>
      <div class="stat-sub">${atRisk.map(e => e.name).join(', ') || 'None'}</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">On Track</div>
      <div class="stat-value" style="color: #28a745">${onTrack.length}</div>
      <div class="stat-sub">${Math.round(onTrack.length / allEngagements.length * 100)}% of portfolio</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">Next Deadline</div>
      <div class="stat-value" style="font-size: 16px; padding-top: 4px;">${nextDeadline ? nextDeadline.name : 'None'}</div>
      <div class="stat-sub">${nextDeadline ? nextDeadline.client + ' · ' + formatDate(nextDeadline.date) : ''}</div>
    </div>
  `;
}

// ============================================================
// RENDER: GANTT TIMELINE
// ============================================================

let currentFilter = 'all';
let selectedId = null;

function setFilter(filter) {
  currentFilter = filter;
  document.querySelectorAll('.filter-btn').forEach(btn => {
    btn.classList.toggle('active', btn.dataset.filter === filter);
  });
  renderGantt();
}

function renderGantt() {
  const container = document.getElementById('ganttContainer');
  const showRetainers = currentFilter === 'all' || currentFilter === 'retainers';
  const showProjects = currentFilter === 'all' || currentFilter === 'projects';

  let html = '';

  if (showRetainers) {
    html += '<div class="gantt-section-title">Retainer Clients</div>';
    html += buildGanttGrid(retainers);
  }
  if (showProjects) {
    html += '<div class="gantt-section-title">Project Engagements</div>';
    html += buildGanttGrid(projects);
  }

  container.innerHTML = html;

  // Add event listeners
  container.querySelectorAll('[data-engagement-id]').forEach(el => {
    el.addEventListener('click', () => selectEngagement(el.dataset.engagementId));
    el.addEventListener('mouseenter', (e) => showTooltip(e, el.dataset.engagementId));
    el.addEventListener('mouseleave', hideTooltip);
  });
}

function buildGanttGrid(items) {
  const monthCols = MONTHS.map(m => `<div class="gantt-month">${m}</div>`).join('');
  const gridTemplateCols = `repeat(${TOTAL_MONTHS}, 1fr)`;

  let rowsHtml = '';
  items.forEach(item => {
    const isSelected = selectedId === item.id;
    const healthClass = item.health;

    // Calculate bar position
    const start = item.sowStart || item.start;
    const end = item.sowEnd || item.end;
    const leftPct = dateToTimelinePercent(start);
    const rightPct = dateToTimelinePercent(end);
    const widthPct = rightPct - leftPct;

    // Clamp to visible area
    const clampedLeft = Math.max(0, leftPct);
    const clampedRight = Math.min(100, rightPct);
    const clampedWidth = clampedRight - clampedLeft;

    const extendsLeft = leftPct < 0;
    const extendsRight = rightPct > 100;

    // Progress fill
    let progressPct = 0;
    if (item.type === 'retainer') {
      progressPct = Math.min(100, (item.actualHrs / item.allocatedHrs) * 100);
    } else {
      progressPct = item.burnPct;
    }

    // Milestones
    let milestonesHtml = '';
    (item.milestones || []).forEach(m => {
      const mPct = dateToTimelinePercent(m.date);
      if (mPct >= clampedLeft && mPct <= clampedRight) {
        const relPct = ((mPct - clampedLeft) / clampedWidth) * 100;
        milestonesHtml += `<div class="milestone ${m.status === 'done' ? 'completed' : m.status === 'overdue' ? 'overdue' : 'upcoming'}" style="left: ${relPct}%" title="${m.name} — ${formatDate(m.date)}"></div>`;
      }
    });

    // Renewal marker
    let renewalHtml = '';
    if (item.renewalDate) {
      const rPct = dateToTimelinePercent(item.renewalDate);
      if (rPct >= clampedLeft && rPct <= clampedRight) {
        const relPct = ((rPct - clampedLeft) / clampedWidth) * 100;
        renewalHtml = `<div class="renewal-marker" style="left: ${relPct}%"></div>`;
      }
    }

    rowsHtml += `
      <div class="gantt-row-label ${isSelected ? 'selected' : ''}" data-engagement-id="${item.id}">
        <div class="rag-dot ${healthClass}"></div>
        <span class="client-name">${item.name}</span>
      </div>
      <div class="gantt-row-timeline" data-engagement-id="${item.id}">
        <div class="month-gridlines" style="display:grid; grid-template-columns: ${gridTemplateCols}">
          ${MONTHS.map(() => '<div class="month-gridline"></div>').join('')}
        </div>
        <div class="gantt-bar-container" style="left: ${clampedLeft}%; width: ${clampedWidth}%;">
          ${extendsLeft ? '<span class="bar-arrow-left">◀</span>' : ''}
          <div class="gantt-bar ${healthClass}">
            <div class="gantt-bar-fill" style="width: ${progressPct}%"></div>
            ${milestonesHtml}
            ${renewalHtml}
          </div>
          ${extendsRight ? '<span class="bar-arrow-right">▶</span>' : ''}
        </div>
        <div class="today-line" style="left: ${dateToTimelinePercent(TODAY)}%"></div>
      </div>
    `;
  });

  return `
    <div class="gantt" style="grid-template-columns: 220px 1fr;">
      <div class="gantt-header-label">Engagement</div>
      <div class="gantt-header-months" style="display: grid; grid-template-columns: ${gridTemplateCols}">
        ${monthCols}
      </div>
      ${rowsHtml}
    </div>
  `;
}

// ============================================================
// TOOLTIP
// ============================================================

function showTooltip(event, id) {
  const item = allEngagements.find(e => e.id === id);
  if (!item) return;
  const tip = document.getElementById('tooltip');

  let content = `<strong>${item.name}</strong><br>`;
  if (item.type === 'retainer') {
    content += `${formatCurrency(item.retainer)}/mo · ${item.utilization}% utilization<br>`;
    content += `Health: ${item.scores.overall}/100`;
  } else {
    content += `${formatCurrency(item.budget)} budget · ${item.burnPct}% burned<br>`;
    content += `${item.healthLabel}`;
  }

  tip.innerHTML = content;
  tip.classList.add('visible');
  tip.style.left = (event.clientX + 12) + 'px';
  tip.style.top = (event.clientY - 10) + 'px';
}

function hideTooltip() {
  document.getElementById('tooltip').classList.remove('visible');
}

document.addEventListener('mousemove', (e) => {
  const tip = document.getElementById('tooltip');
  if (tip.classList.contains('visible')) {
    tip.style.left = (e.clientX + 12) + 'px';
    tip.style.top = (e.clientY - 10) + 'px';
  }
});

// ============================================================
// DETAIL PANEL
// ============================================================

function selectEngagement(id) {
  if (selectedId === id) {
    selectedId = null;
    document.getElementById('detailPanel').classList.remove('visible');
    renderGantt();
    return;
  }
  selectedId = id;
  renderGantt();
  renderDetail(id);
}

function renderDetail(id) {
  const item = allEngagements.find(e => e.id === id);
  if (!item) return;
  const panel = document.getElementById('detailPanel');

  // Budget section
  let budgetHtml = '';
  if (item.type === 'retainer') {
    const burnClass = item.utilization > 110 ? 'danger' : item.utilization > 100 ? 'warning' : 'healthy';
    budgetHtml = `
      <div class="budget-row"><span class="label">Monthly Retainer</span><span class="value">${formatCurrency(item.retainer)}</span></div>
      <div class="budget-row"><span class="label">Allocated Hours</span><span class="value">${item.allocatedHrs} hrs</span></div>
      <div class="budget-row"><span class="label">Actual Hours</span><span class="value">${item.actualHrs} hrs</span></div>
      <div class="budget-bar"><div class="budget-bar-fill ${burnClass}" style="width: ${Math.min(100, item.utilization)}%"></div></div>
      <div class="budget-row"><span class="label">Utilization</span><span class="value">${item.utilization}%</span></div>
      <div class="budget-row"><span class="label">Effective Rate</span><span class="value">${formatCurrency(item.effectiveRate)}/hr</span></div>
    `;
  } else {
    const burnClass = item.burnPct > 85 ? 'danger' : item.burnPct > 70 ? 'warning' : 'healthy';
    budgetHtml = `
      <div class="budget-row"><span class="label">Total Budget</span><span class="value">${formatCurrency(item.budget)}</span></div>
      <div class="budget-row"><span class="label">Burned</span><span class="value">${formatCurrency(item.burned)} (${item.burnPct}%)</span></div>
      <div class="budget-row"><span class="label">Remaining</span><span class="value">${formatCurrency(item.budget - item.burned)}</span></div>
      <div class="budget-bar"><div class="budget-bar-fill ${burnClass}" style="width: ${item.burnPct}%"></div></div>
      <div class="budget-row"><span class="label">Timeline</span><span class="value">${formatMonthYear(item.start)} – ${formatMonthYear(item.end)}</span></div>
    `;
  }

  // Health scores (retainers only)
  let healthHtml = '';
  if (item.scores) {
    healthHtml = `
      <div class="health-scores">
        <div class="health-score">
          <div class="health-score-value ${healthColor(item.scores.performance)}">${item.scores.performance}</div>
          <div class="health-score-label">Performance</div>
        </div>
        <div class="health-score">
          <div class="health-score-value ${healthColor(item.scores.profitability)}">${item.scores.profitability}</div>
          <div class="health-score-label">Profitability</div>
        </div>
        <div class="health-score">
          <div class="health-score-value ${healthColor(item.scores.relationship)}">${item.scores.relationship}</div>
          <div class="health-score-label">Relationship</div>
        </div>
        <div class="health-score">
          <div class="health-score-value ${healthColor(item.scores.overall)}" style="font-size: 28px;">${item.scores.overall}</div>
          <div class="health-score-label">Overall</div>
        </div>
      </div>
    `;
  } else {
    healthHtml = `<p style="color: #888; font-size: 13px;">Project engagement — health scores apply to retainer clients</p>`;
  }

  // Milestones
  const milestonesHtml = (item.milestones || []).map(m => {
    const iconClass = m.status === 'done' ? 'done' : m.status === 'in-progress' ? 'in-progress' : m.status === 'overdue' ? 'overdue' : 'upcoming';
    const icon = m.status === 'done' ? '✓' : m.status === 'in-progress' ? '●' : m.status === 'overdue' ? '!' : '○';
    return `
      <li class="milestone-item">
        <div class="milestone-icon ${iconClass}">${icon}</div>
        <span>${m.name}</span>
        <span class="milestone-date">${formatDate(m.date)}</span>
      </li>
    `;
  }).join('');

  // Team
  const teamHtml = item.team.map(t =>
    `<span class="team-member">${t.name} <span class="role">· ${t.role}</span></span>`
  ).join('');

  // Risks
  let risksHtml = '';
  if (item.risks && item.risks.length > 0) {
    risksHtml = item.risks.map(r =>
      `<div class="risk-note ${item.health === 'red' ? 'critical' : ''}">${r}</div>`
    ).join('');
  } else {
    risksHtml = '<p style="color: #888; font-size: 13px;">No active risk notes</p>';
  }

  panel.innerHTML = `
    <div class="detail-header">
      <div class="detail-header-left">
        <span class="detail-client-name">${item.name}</span>
        <span class="detail-badge ${item.health}">${item.healthLabel}</span>
      </div>
      <button class="detail-close" onclick="selectEngagement('${item.id}')">&times;</button>
    </div>
    <div class="detail-body">
      <div class="detail-section">
        <div class="detail-section-title">Budget</div>
        ${budgetHtml}
      </div>
      <div class="detail-section">
        <div class="detail-section-title">Health Scores</div>
        ${healthHtml}
      </div>
      <div class="detail-section">
        <div class="detail-section-title">Milestones</div>
        <ul class="milestone-list">${milestonesHtml}</ul>
      </div>
      <div class="detail-section">
        <div class="detail-section-title">Team</div>
        <div class="team-list">${teamHtml}</div>
      </div>
      <div class="detail-section" style="grid-column: 1 / -1; border-right: none;">
        <div class="detail-section-title">Risk Notes</div>
        ${risksHtml}
      </div>
    </div>
  `;
  panel.classList.add('visible');
  panel.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
}

// ============================================================
// INIT
// ============================================================

renderSummary();
renderGantt();
</script>

</body>
</html>
```

- [ ] **Step 2: Open in browser and verify**

Run: `open tools/project_timeline.html`

Expected:
- Branded dark header with "Sidebar Creative" and "Project Timeline Dashboard"
- Summary stats row with 4 cards
- Filter buttons (All/Retainers/Projects)
- Gantt timeline with two sections, 12 rows total
- Red "Today" vertical line at April 7
- Clicking any row opens detail panel below
- Hovering shows tooltip
- Filter buttons toggle sections
- Detail panel shows budget, health scores, milestones, team, risks

- [ ] **Step 3: Commit**

```bash
git add tools/project_timeline.html
git commit -m "Add interactive project timeline dashboard with Gantt view and detail panels"
```

---

### Task 2: Process Log and Tutorial

**Files:**
- Modify: `docs/process_log.md`
- Create: `tutorials/02_project_timeline.md`

- [ ] **Step 1: Append process log entry**

Add to top of `docs/process_log.md`:

```markdown
## 2026-04-07 — Project Timeline Dashboard

**Prompt:** Build a project timeline dashboard for Sidebar Creative. Read the brand definition and client portfolio data. Show all active projects on an interactive Gantt-style timeline with start dates, deadlines, current progress, and RAG status. Include the 8 retainer clients and the project-based engagements. Output as an interactive HTML file.

**What was built:** Interactive Gantt-style timeline dashboard (`tools/project_timeline.html`) showing all 12 active Sidebar Creative engagements — 8 retainer clients and 4 project-based engagements.

**Key decisions:**
- Single self-contained HTML file with all CSS/JS inlined
- Timeline + Detail Panel layout: compact Gantt on top, click-to-expand detail below
- Branded header only; clean practical body for PM use
- Retainers show current SOW term with renewal date markers
- Progress shows both budget burn and milestone markers
- RAG status coloring on bars and dots
- Today line, tooltips, and filter toggles for interactivity

**Files created/modified:**
- `tools/project_timeline.html` (created)
- `docs/process_log.md` (modified)
- `tutorials/02_project_timeline.md` (created)
```

- [ ] **Step 2: Write tutorial**

Create `tutorials/02_project_timeline.md` with step-by-step recreation guide covering: the brainstorming prompts used, design decisions (layout choice, style choice, progress tracking choice), data extraction from brand definition, and the final implementation approach.

- [ ] **Step 3: Commit**

```bash
git add docs/process_log.md tutorials/02_project_timeline.md
git commit -m "Add process log and tutorial for project timeline dashboard"
```
