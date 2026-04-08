# Status Report Generator Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an interactive HTML tool that generates and displays weekly status reports for all 8 retainer clients plus an internal Monday leadership roll-up, with copy-to-markdown functionality.

**Architecture:** Single self-contained HTML file with two-panel layout (selector + viewer). Report content is pre-authored as JavaScript data objects with structured sections (Progress, Blockers, Next Steps, Budget Health, Key Metrics). The tool renders reports as formatted HTML and provides a "Copy Markdown" button. All data embedded — no external dependencies or API calls.

**Tech Stack:** HTML, CSS, vanilla JavaScript — zero external dependencies.

**Spec:** `docs/superpowers/specs/2026-04-08-status-report-generator-design.md`

**Data sources:**
- `brand/brand_definition.md` — Client details, risk notes, team assignments, health scores
- `data/client_sow_terms.md` — SOW hours, deliverables, key terms
- `data/client_performance_history.md` — 6 months of metrics (March 2026 = latest)
- `data/client_health_framework.md` — Health scoring methodology and thresholds

---

### Task 1: HTML Scaffold with Header, Stats, and Two-Panel Layout

**Files:**
- Create: `tools/status_reports.html`

- [ ] **Step 1: Create the HTML file with all CSS and layout HTML**

Create `tools/status_reports.html` with:

**CSS:** Reset/base matching existing tools, plus:
- `.header`, `.stats-row`, `.stat-card` — same as other tools
- `.layout` — flexbox container for the two panels
- `.selector-panel` — left panel (flex: 0 0 300px), scrollable list of reports
- `.viewer-panel` — right panel (flex: 1), report display area
- `.report-item` — clickable row in selector (name, health dot, meta)
- `.report-item.active` — highlighted with blue left border
- `.rollup-item` — special dark styling for the roll-up entry at top
- `.section-label` — category headers in selector ("Client Reports")
- `.report-header` — report title bar with copy button
- `.report-body` — report content area with generous line-height
- `.report-section` — section with heading and bottom border
- `.report-table` — clean data table for budget and metrics
- `.copy-btn` — green button, transitions to "Copied!" state
- `.health-badge` — colored badge (green/yellow/red) with score

**HTML body:**
```html
<div class="header">
  <div class="header-left">
    <div class="header-brand">Sidebar Creative</div>
    <div class="header-title">Status Reports</div>
  </div>
  <div class="header-date" id="headerDate"></div>
</div>
<div class="stats-row">
  <div class="stat-card">
    <div class="stat-label">Reports Generated</div>
    <div class="stat-value" id="statReports">9</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Red Accounts</div>
    <div class="stat-value red" id="statRed">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Yellow Accounts</div>
    <div class="stat-value yellow" id="statYellow">—</div>
  </div>
  <div class="stat-card">
    <div class="stat-label">Avg Health Score</div>
    <div class="stat-value" id="statAvgHealth">—</div>
  </div>
</div>
<div class="layout">
  <div class="selector-panel" id="selectorPanel"></div>
  <div class="viewer-panel" id="viewerPanel"></div>
</div>
```

- [ ] **Step 2: Verify scaffold renders**

Run: `open tools/status_reports.html`

Expected: Dark header, 4 stat cards, empty two-panel layout.

- [ ] **Step 3: Commit**

```bash
git add tools/status_reports.html
git commit -m "Add status report generator scaffold with two-panel layout"
```

---

### Task 2: Client Data and Report Content

**Files:**
- Modify: `tools/status_reports.html`

Add the `<script>` block with all client data and pre-authored report content. This is the largest task — it contains all 8 client report objects and the roll-up content.

- [ ] **Step 1: Add the client data array**

Add a `<script>` tag before `</body>`. Each client object contains everything needed to render their report:

```javascript
const REPORT_DATE = 'Week of March 31 – April 4, 2026';

const CLIENTS = [
  {
    id: 'evergreen',
    name: 'Evergreen Outdoor Co.',
    health: 91,
    status: 'green',
    retainer: 45000,
    accountDirector: 'Sophie Morales',
    progress: [
      'Spring campaign creative delivered — 4 hero assets, 12 social variants across Meta, Pinterest, and TikTok',
      'Shopify Plus checkout optimization A/B test launched; targeting 5% conversion lift',
      'March email program (9 sends) achieved 29.6% open rate and 4.5% click rate, generating $168K attributed revenue',
      'Pinterest strategy driving 1.56M monthly views, up 9.9% MoM',
      'Instagram grew to 145,800 followers (+3.3% MoM) with 4.0% engagement rate'
    ],
    blockers: [
      'Awaiting client approval on Q2 campaign brief (sent Mar 28, follow-up scheduled Apr 8)',
      'Summer collection product photography delayed — vendor ETA April 15'
    ],
    nextSteps: [
      'Launch spring paid media campaign across Meta and Google ($85K/month media spend)',
      'Begin summer collection landing page design once photography arrives',
      'Prep quarterly business review (scheduled Apr 18 with CMO)',
      'Publish 8 blog posts and 20 social posts per content calendar'
    ],
    budget: { sowHours: 257, actualHours: 252, utilization: 98, monthlyBudget: 45000, actualCost: 44100, budgetStatus: 'On budget' },
    metrics: [
      { name: 'Website Sessions', value: '325,800', change: '+9.1%', direction: 'up' },
      { name: 'MQLs Generated', value: '2,280', change: '+18.8%', direction: 'up' },
      { name: 'Email Open Rate', value: '29.6%', change: '+1.5pp', direction: 'up' },
      { name: 'Email Revenue', value: '$168,000', change: '+21.7%', direction: 'up' },
      { name: 'Instagram Followers', value: '145,800', change: '+3.3%', direction: 'up' },
      { name: 'Pinterest Monthly Views', value: '1,560,000', change: '+9.9%', direction: 'up' }
    ]
  },
  {
    id: 'brightwash',
    name: 'Brightwash Home',
    health: 78,
    status: 'green',
    retainer: 38000,
    accountDirector: 'Tomás Reyes',
    progress: [
      'Subscription platform optimization — checkout flow refinements reduced abandonment by 8%',
      'March email program (13 sends) achieved 26.0% open rate and 3.7% click rate, $74K attributed revenue',
      'Social creative package delivered — 16 posts across Instagram and Facebook',
      'A/B test on product landing page completed; variant B selected (+12% add-to-cart rate)',
      'Website traffic up 4.2% MoM to 180,100 sessions'
    ],
    blockers: [
      'Untracked scope creep continues — small client requests accumulating without change orders (est. 12-16 hrs/mo unbilled)',
      'Subscription module integration with Klaviyo experiencing intermittent sync delays'
    ],
    nextSteps: [
      'Implement Klaviyo sync fix (dev ticket prioritized for Apr 7-11)',
      'Launch April email campaign calendar (12 planned sends)',
      'Begin competitive analysis refresh for Q2 strategy deck',
      'Scope conversation needed with client re: untracked requests'
    ],
    budget: { sowHours: 224, actualHours: 236, utilization: 105, monthlyBudget: 38000, actualCost: 40120, budgetStatus: 'Over budget' },
    metrics: [
      { name: 'Website Sessions', value: '180,100', change: '+4.2%', direction: 'up' },
      { name: 'MQLs Generated', value: '1,180', change: '+7.3%', direction: 'up' },
      { name: 'Email Open Rate', value: '26.0%', change: '+0.6pp', direction: 'up' },
      { name: 'Email Revenue', value: '$74,000', change: '+8.8%', direction: 'up' },
      { name: 'Instagram Followers', value: '55,100', change: '+2.8%', direction: 'up' },
      { name: 'Subscription Signups', value: '1,680', change: '+6.3%', direction: 'up' }
    ]
  },
  {
    id: 'vello',
    name: 'Vello Cycling',
    health: 44,
    status: 'red',
    retainer: 32000,
    accountDirector: 'Sophie Morales',
    progress: [
      'Delivered revised Q1 campaign creative per new VP Marketing direction (3rd round of revisions)',
      'March email sends (6 total) — open rate declined to 16.5%, click rate 1.6%',
      'Website maintenance and performance updates completed on schedule',
      'Derek Okafor now attending all client calls as part of relationship recovery plan'
    ],
    blockers: [
      'CRITICAL: Client has requested competitive agency pitches — 60-day window to demonstrate value',
      'New VP Marketing (hired Oct 2025) continues to reject creative direction, causing rework cycles',
      'All key metrics declining for 6 consecutive months — traffic down 24%, MQLs down 38%, email revenue down 61%',
      'Hours consistently 15-19% over SOW due to rework — account is unprofitable at $152/hr effective rate'
    ],
    nextSteps: [
      'Present relationship recovery plan to VP Marketing (scheduled Apr 10)',
      'Prepare competitive differentiation deck highlighting Sidebar\'s brand expertise',
      'Propose SOW restructure — shift budget from content to performance marketing',
      'Schedule weekly check-ins with client (increased from bi-weekly)'
    ],
    budget: { sowHours: 183, actualHours: 215, utilization: 117, monthlyBudget: 32000, actualCost: 37625, budgetStatus: 'Over budget' },
    metrics: [
      { name: 'Website Sessions', value: '108,200', change: '-3.7%', direction: 'down' },
      { name: 'MQLs Generated', value: '420', change: '-6.7%', direction: 'down' },
      { name: 'Email Open Rate', value: '16.5%', change: '-0.7pp', direction: 'down' },
      { name: 'Email Revenue', value: '$15,000', change: '-16.7%', direction: 'down' },
      { name: 'Instagram Followers', value: '60,200', change: '-1.0%', direction: 'down' },
      { name: 'Cost per Lead', value: '$52.00', change: '+7.9%', direction: 'down' }
    ]
  },
  {
    id: 'petal',
    name: 'Petal & Thorn',
    health: 86,
    status: 'green',
    retainer: 28000,
    accountDirector: 'Alina Park',
    progress: [
      'Post-Valentine\'s wind-down complete — campaign delivered $148K attributed email revenue (client\'s best ever)',
      'Transitioned back to base SOW hours from seasonal surge (238→170 hrs/mo)',
      'March email program (6 sends) at 27.0% open rate — normalizing after Valentine\'s peak',
      'Shopify seasonal landing pages archived and spring collection pages staged',
      'Instagram reached 50,800 followers (+2.4% MoM)'
    ],
    blockers: [
      'No active blockers this week'
    ],
    nextSteps: [
      'Begin Mother\'s Day campaign planning (launch window: late April)',
      'Mother\'s Day email sequence build-out (12 sends planned for May)',
      'Spring product photography shoot coordination (Apr 14-16)',
      'Seasonal promotional calendar review with client (Apr 11)'
    ],
    budget: { sowHours: 170, actualHours: 158, utilization: 93, monthlyBudget: 28000, actualCost: 26070, budgetStatus: 'Under budget' },
    metrics: [
      { name: 'Website Sessions', value: '95,600', change: '-44.5%', direction: 'down' },
      { name: 'MQLs Generated', value: '560', change: '-56.3%', direction: 'down' },
      { name: 'Email Revenue', value: '$45,000', change: '-69.6%', direction: 'down' },
      { name: 'Instagram Followers', value: '50,800', change: '+2.4%', direction: 'up' },
      { name: 'Pinterest Monthly Views', value: '680,000', change: '-39.3%', direction: 'down' },
      { name: 'TikTok Followers', value: '33,600', change: '+2.4%', direction: 'up' }
    ],
    metricsNote: 'March decline is expected seasonal normalization after Valentine\'s Day peak in February.'
  },
  {
    id: 'kova',
    name: 'Kova Skincare',
    health: 64,
    status: 'yellow',
    retainer: 25000,
    accountDirector: 'Tomás Reyes',
    progress: [
      'Web redesign (launched Feb) showing early UX improvements — bounce rate down to 45.8% (-0.4pp), pages/session up to 3.1',
      'March email program (9 sends) at 21.0% open rate and 2.5% click rate, $30K attributed revenue',
      'CRO roadmap delivered to client — 6 optimization experiments planned for Q2',
      'Social content cadence maintained — 12 posts delivered across Instagram and TikTok'
    ],
    blockers: [
      'Conversion rate and lead generation remain flat despite redesign — client\'s 15% traffic growth and 10% conversion targets not being met',
      'Client questioning ROI at current retainer level — possible downsize from $25K to $18K at July quarterly review',
      'Paid acquisition costs rising — cost per lead at $28.80, up from $27.20 in December'
    ],
    nextSteps: [
      'Launch first CRO experiment (product page social proof test) Apr 8',
      'Prepare mid-quarter ROI analysis for client presentation (Apr 15)',
      'Klaviyo integration optimization — improve abandoned cart flow conversion',
      'Develop "quick wins" plan to demonstrate value before July review'
    ],
    budget: { sowHours: 147, actualHours: 150, utilization: 102, monthlyBudget: 25000, actualCost: 25500, budgetStatus: 'On budget' },
    metrics: [
      { name: 'Website Sessions', value: '101,800', change: '+1.6%', direction: 'up' },
      { name: 'MQLs Generated', value: '570', change: '+1.8%', direction: 'flat' },
      { name: 'Conversion Rate', value: '13.0%', change: '+0.5pp', direction: 'flat' },
      { name: 'Email Revenue', value: '$30,000', change: '+3.4%', direction: 'up' },
      { name: 'Instagram Followers', value: '76,200', change: '+1.1%', direction: 'flat' },
      { name: 'Cost per Lead', value: '$28.80', change: '-2.7%', direction: 'up' }
    ]
  },
  {
    id: 'drift',
    name: 'Drift Coffee Roasters',
    health: 83,
    status: 'green',
    retainer: 22000,
    accountDirector: 'Priya Nair',
    progress: [
      'Subscription platform optimization — 290 new subscription signups in March (+8.2% MoM)',
      'March email program (5 sends) achieved 29.5% open rate and 4.4% click rate, $21.2K revenue',
      'Annual brand refresh kickoff completed — mood boards approved, moving to identity refinements',
      'Google Reviews reached 4.8 stars (1,470 reviews, +45 in March)',
      'Website traffic up 3.6% MoM to 74,200 sessions'
    ],
    blockers: [
      'No active blockers this week'
    ],
    nextSteps: [
      'Deliver brand refresh concepts (round 1 due Apr 14)',
      'Launch spring subscription promotion campaign',
      'Publish 3 blog posts and 10 social posts per content calendar',
      'Shopify performance audit — page speed optimization sprint'
    ],
    budget: { sowHours: 138, actualHours: 130, utilization: 94, monthlyBudget: 22000, actualCost: 20800, budgetStatus: 'Under budget' },
    metrics: [
      { name: 'Website Sessions', value: '74,200', change: '+3.6%', direction: 'up' },
      { name: 'Subscription Signups', value: '290', change: '+8.2%', direction: 'up' },
      { name: 'MQLs Generated', value: '430', change: '+6.2%', direction: 'up' },
      { name: 'Email Revenue', value: '$21,200', change: '+14.0%', direction: 'up' },
      { name: 'Instagram Followers', value: '36,800', change: '+2.5%', direction: 'up' },
      { name: 'Google Reviews', value: '4.8★ (1,470)', change: '+45 new', direction: 'up' }
    ]
  },
  {
    id: 'summit',
    name: 'Summit Pet Supply',
    health: 84,
    status: 'green',
    retainer: 18000,
    accountDirector: 'James Whitfield',
    progress: [
      'Website traffic grew 8.7% MoM to 72,600 sessions — strongest month since engagement began',
      'MQLs up 11.4% to 440 with conversion rate improving to 17.0%',
      'March email program (5 sends) at 28.8% open rate, $22.6K attributed revenue (+17.7% MoM)',
      'Instagram surpassed 30K followers (+10.1% MoM), TikTok reached 20,400 (+14.6%)',
      'New feature build for product recommendation engine launched'
    ],
    blockers: [
      'Client has asked about expanding scope twice — current $18K retainer may be limiting growth potential'
    ],
    nextSteps: [
      'Prepare scope expansion proposal ($25-30K retainer with added paid media management)',
      'Launch spring pet essentials campaign (content + email + social)',
      'Continue Shopify feature builds per roadmap (loyalty program next)',
      'Publish 4 blog posts and 8 social posts per content calendar'
    ],
    budget: { sowHours: 113, actualHours: 108, utilization: 96, monthlyBudget: 18000, actualCost: 17280, budgetStatus: 'On budget' },
    metrics: [
      { name: 'Website Sessions', value: '72,600', change: '+8.7%', direction: 'up' },
      { name: 'MQLs Generated', value: '440', change: '+11.4%', direction: 'up' },
      { name: 'Conversion Rate', value: '17.0%', change: '+1.1pp', direction: 'up' },
      { name: 'Email Revenue', value: '$22,600', change: '+17.7%', direction: 'up' },
      { name: 'Instagram Followers', value: '30,600', change: '+10.1%', direction: 'up' },
      { name: 'TikTok Followers', value: '20,400', change: '+14.6%', direction: 'up' }
    ]
  },
  {
    id: 'moonridge',
    name: 'Moonridge Spirits',
    health: 60,
    status: 'yellow',
    retainer: 12000,
    accountDirector: 'Alina Park',
    progress: [
      'Brand identity development continuing — bourbon line visual system in 5th round of revisions (contracted for 3)',
      'Website traffic growing steadily — 10,200 sessions in March (+21.4% MoM) ahead of full launch',
      'Social media presence building — Instagram at 6,800 followers, strong 5.0% engagement rate',
      'March event (tasting room pop-up) drew 145 attendees, generating 420 new email subscribers'
    ],
    blockers: [
      'Brand identity phase now in month 5 (budgeted for 3) — founding team indecisiveness driving excessive revision cycles (avg 5 rounds vs. 3 contracted)',
      'Account is marginally profitable at best — averaging 88 actual hrs/mo against 77 SOW hrs (114% utilization)',
      'Effective hourly rate at $136/hr, well below $155/hr blended rate — account likely unprofitable in 2 of 4 months'
    ],
    nextSteps: [
      'Set firm deadline for brand identity sign-off (propose Apr 18 as final)',
      'Prepare full website launch plan (target: late April)',
      'Scope discussion needed: transition from brand phase to ongoing retainer deliverables',
      'Plan April tasting room event (client wants monthly cadence)'
    ],
    budget: { sowHours: 77, actualHours: 82, utilization: 106, monthlyBudget: 12000, actualCost: 12710, budgetStatus: 'Over budget' },
    metrics: [
      { name: 'Website Sessions', value: '10,200', change: '+21.4%', direction: 'up' },
      { name: 'MQLs Generated', value: '105', change: '+19.3%', direction: 'up' },
      { name: 'Email Subscribers', value: '4,600', change: '+21.1%', direction: 'up' },
      { name: 'Instagram Followers', value: '6,800', change: '+17.2%', direction: 'up' },
      { name: 'Event Attendees', value: '145', change: '+31.8%', direction: 'up' },
      { name: 'Email Revenue', value: '$6,500', change: '+12.1%', direction: 'up' }
    ]
  }
];
```

- [ ] **Step 2: Add the roll-up report content**

```javascript
const ROLLUP = {
  title: 'Monday Leadership Roll-Up',
  date: REPORT_DATE,
  preparedFor: 'Maya Chen, Raj Patel, Lena Voss, Derek Okafor',
  attentionClients: [
    {
      id: 'vello',
      name: 'Vello Cycling',
      health: 44,
      status: 'red',
      highlights: [
        'CHURN RISK: Client has requested competitive agency pitches — 60-day window to retain',
        'All key metrics declining for 6 consecutive months (traffic -24%, MQLs -38%, email revenue -61% since Oct)',
        'New VP Marketing (Oct 2025) rejecting creative direction, driving rework cycles — hours 17% over SOW',
        'Recovery plan: Derek attending all calls, restructured SOW proposal due Apr 10, competitive differentiation deck in progress'
      ]
    },
    {
      id: 'kova',
      name: 'Kova Skincare',
      health: 64,
      status: 'yellow',
      highlights: [
        'ROI CONCERN: Web redesign (launched Feb) has not moved conversion metrics — client questioning value',
        'Performance milestones not being met (15% traffic growth target, 10% conversion improvement)',
        'Possible retainer downsize from $25K to $18K at July quarterly review',
        'Action: CRO experiment launching Apr 8, mid-quarter ROI analysis for client by Apr 15'
      ]
    },
    {
      id: 'moonridge',
      name: 'Moonridge Spirits',
      health: 60,
      status: 'yellow',
      highlights: [
        'MARGIN PRESSURE: Brand identity phase now month 5 of 3 — revision cycles 2x contracted limit',
        'Account averaging 114% of SOW hours, effective rate at $136/hr (target: $155/hr)',
        'Likely unprofitable in 2 of 4 operating months — team absorbing overages',
        'Action: Firm brand sign-off deadline proposed for Apr 18, scope transition plan needed'
      ]
    }
  ],
  onTrackClients: [
    { name: 'Evergreen Outdoor Co.', health: 91, retainer: 45000, note: 'Expanded retainer performing well; spring campaign launching, QBR Apr 18' },
    { name: 'Petal & Thorn', health: 86, retainer: 28000, note: 'Strong Valentine\'s results ($148K email rev); transitioning to Mother\'s Day planning' },
    { name: 'Summit Pet Supply', health: 84, retainer: 18000, note: 'Strongest growth metrics in portfolio; scope expansion proposal in prep ($25-30K)' },
    { name: 'Drift Coffee Roasters', health: 83, retainer: 22000, note: 'Steady performer; brand refresh underway, subscription signups growing' },
    { name: 'Brightwash Home', health: 78, retainer: 38000, note: 'Metrics healthy but 5% over SOW hours — scope creep conversation needed' }
  ],
  portfolioSummary: {
    totalRevenue: 220000,
    avgHealth: 74,
    blendedUtilization: 103,
    greenCount: 5,
    yellowCount: 2,
    redCount: 1,
    overSOW: ['Brightwash Home (105%)', 'Vello Cycling (117%)', 'Moonridge Spirits (106%)']
  }
};
```

- [ ] **Step 3: Add utility functions**

```javascript
const HEALTH_COLORS = { green: '#28a745', yellow: '#ffc107', red: '#dc3545' };
const STATUS_LABELS = { green: 'Healthy', yellow: 'Watch', red: 'At Risk' };
const STATUS_EMOJI = { green: '🟢', yellow: '🟡', red: '🔴' };

function formatMoney(n) { return '$' + n.toLocaleString(); }

let selectedReport = 'rollup';
```

- [ ] **Step 4: Commit**

```bash
git add tools/status_reports.html
git commit -m "Add client report data and roll-up content for all 8 retainer clients"
```

---

### Task 3: Render Selector Panel and Report Viewer

**Files:**
- Modify: `tools/status_reports.html`

Build the selector panel with the roll-up at top and 8 client entries below. Build the report rendering functions for both client reports and the roll-up.

- [ ] **Step 1: Write renderSelector()**

```javascript
function renderSelector() {
  let html = '';
  // Roll-up entry
  html += `<div class="rollup-item ${selectedReport === 'rollup' ? 'active' : ''}" onclick="selectReport('rollup')">`;
  html += '<div class="rollup-title">Monday Leadership Roll-Up</div>';
  html += `<div class="rollup-meta">${REPORT_DATE}</div>`;
  html += '</div>';
  // Section label
  html += '<div class="section-label">Client Reports</div>';
  // Client entries
  CLIENTS.forEach(client => {
    const isActive = selectedReport === client.id;
    html += `<div class="report-item ${isActive ? 'active' : ''}" onclick="selectReport('${client.id}')">`;
    html += '<div class="report-item-row">';
    html += `<span class="health-dot" style="background:${HEALTH_COLORS[client.status]};"></span>`;
    html += `<span class="report-item-name">${client.name}</span>`;
    html += '</div>';
    html += `<div class="report-item-meta">${formatMoney(client.retainer)}/mo · Health: ${client.health}</div>`;
    html += '</div>';
  });
  document.getElementById('selectorPanel').innerHTML = html;
}

function selectReport(id) {
  selectedReport = id;
  renderSelector();
  renderViewer();
}
```

- [ ] **Step 2: Write renderViewer() for client reports**

```javascript
function renderViewer() {
  if (selectedReport === 'rollup') {
    renderRollup();
    return;
  }
  const client = CLIENTS.find(c => c.id === selectedReport);
  if (!client) return;

  let html = '<div class="report-header">';
  html += `<div class="report-title">${client.name} — Weekly Status Report</div>`;
  html += `<button class="copy-btn" onclick="copyMarkdown()">Copy Markdown</button>`;
  html += '</div>';
  html += '<div class="report-body">';
  html += `<div class="report-date">${REPORT_DATE} · Account Director: ${client.accountDirector} · `;
  html += `<span class="health-badge" style="background:${HEALTH_COLORS[client.status]}20;color:${HEALTH_COLORS[client.status]};">Health: ${client.health} ${STATUS_EMOJI[client.status]}</span></div>`;

  // Progress
  html += '<div class="report-section"><h3>Progress</h3><ul>';
  client.progress.forEach(item => { html += `<li>${item}</li>`; });
  html += '</ul></div>';

  // Blockers
  html += '<div class="report-section"><h3>Blockers</h3><ul>';
  client.blockers.forEach(item => { html += `<li>${item}</li>`; });
  html += '</ul></div>';

  // Next Steps
  html += '<div class="report-section"><h3>Next Steps</h3><ul>';
  client.nextSteps.forEach(item => { html += `<li>${item}</li>`; });
  html += '</ul></div>';

  // Budget Health
  const b = client.budget;
  const utilColor = b.utilization <= 100 ? '#28a745' : (b.utilization <= 110 ? '#ffc107' : '#dc3545');
  html += '<div class="report-section"><h3>Budget Health</h3>';
  html += '<table class="report-table"><thead><tr><th>Metric</th><th>SOW</th><th>Actual</th><th>Status</th></tr></thead><tbody>';
  html += `<tr><td>Monthly Hours</td><td>${b.sowHours}</td><td>${b.actualHours}</td><td style="color:${utilColor};">${b.utilization}%</td></tr>`;
  html += `<tr><td>Monthly Budget</td><td>${formatMoney(b.monthlyBudget)}</td><td>${formatMoney(b.actualCost)}</td><td style="color:${utilColor};">${b.budgetStatus}</td></tr>`;
  html += '</tbody></table></div>';

  // Key Metrics
  html += '<div class="report-section"><h3>Key Metrics (March 2026)</h3>';
  if (client.metricsNote) {
    html += `<div class="metrics-note">${client.metricsNote}</div>`;
  }
  html += '<table class="report-table"><thead><tr><th>Metric</th><th>Value</th><th>MoM Change</th></tr></thead><tbody>';
  client.metrics.forEach(m => {
    const changeColor = m.direction === 'up' ? '#28a745' : (m.direction === 'down' ? '#dc3545' : '#555');
    html += `<tr><td>${m.name}</td><td>${m.value}</td><td style="color:${changeColor};">${m.change}</td></tr>`;
  });
  html += '</tbody></table></div>';

  html += '</div>';
  document.getElementById('viewerPanel').innerHTML = html;
}
```

- [ ] **Step 3: Write renderRollup() for the leadership report**

```javascript
function renderRollup() {
  let html = '<div class="report-header">';
  html += '<div class="report-title">Monday Leadership Roll-Up</div>';
  html += `<button class="copy-btn" onclick="copyMarkdown()">Copy Markdown</button>`;
  html += '</div>';
  html += '<div class="report-body">';
  html += `<div class="report-date">${ROLLUP.date} · Prepared for: ${ROLLUP.preparedFor}</div>`;

  // Accounts Requiring Attention
  html += '<div class="report-section"><h3>Accounts Requiring Attention</h3>';
  ROLLUP.attentionClients.forEach(ac => {
    html += `<div class="attention-client">`;
    html += `<div class="attention-header"><span class="health-badge" style="background:${HEALTH_COLORS[ac.status]}20;color:${HEALTH_COLORS[ac.status]};">${ac.name} — ${ac.health} ${STATUS_EMOJI[ac.status]}</span></div>`;
    html += '<ul>';
    ac.highlights.forEach(h => { html += `<li>${h}</li>`; });
    html += '</ul></div>';
  });
  html += '</div>';

  // Accounts On Track
  html += '<div class="report-section"><h3>Accounts On Track</h3>';
  html += '<table class="report-table"><thead><tr><th>Client</th><th>Health</th><th>Retainer</th><th>Status Note</th></tr></thead><tbody>';
  ROLLUP.onTrackClients.forEach(c => {
    html += `<tr><td>${c.name}</td><td><span style="color:#28a745;">${c.health} 🟢</span></td><td>${formatMoney(c.retainer)}/mo</td><td>${c.note}</td></tr>`;
  });
  html += '</tbody></table></div>';

  // Portfolio Summary
  const ps = ROLLUP.portfolioSummary;
  html += '<div class="report-section"><h3>Portfolio Summary</h3>';
  html += '<table class="report-table"><thead><tr><th>Metric</th><th>Value</th></tr></thead><tbody>';
  html += `<tr><td>Total Monthly Retainer Revenue</td><td>${formatMoney(ps.totalRevenue)}</td></tr>`;
  html += `<tr><td>Average Health Score</td><td>${ps.avgHealth}</td></tr>`;
  html += `<tr><td>Blended Utilization</td><td>${ps.blendedUtilization}%</td></tr>`;
  html += `<tr><td>Account Status</td><td>${ps.greenCount} 🟢 / ${ps.yellowCount} 🟡 / ${ps.redCount} 🔴</td></tr>`;
  html += `<tr><td>Accounts Over SOW Hours</td><td>${ps.overSOW.join(', ')}</td></tr>`;
  html += '</tbody></table></div>';

  html += '</div>';
  document.getElementById('viewerPanel').innerHTML = html;
}
```

- [ ] **Step 4: Write updateStats() and initial render**

```javascript
function updateStats() {
  const redCount = CLIENTS.filter(c => c.status === 'red').length;
  const yellowCount = CLIENTS.filter(c => c.status === 'yellow').length;
  const avgHealth = Math.round(CLIENTS.reduce((sum, c) => sum + c.health, 0) / CLIENTS.length);
  document.getElementById('statRed').textContent = redCount;
  document.getElementById('statYellow').textContent = yellowCount;
  document.getElementById('statAvgHealth').textContent = avgHealth;
}

document.getElementById('headerDate').textContent = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
renderSelector();
renderViewer();
updateStats();
```

- [ ] **Step 5: Verify the reports render**

Open the page. Expected: Roll-up selected by default, showing the issues-first leadership report. Click "Evergreen Outdoor Co." in the left panel — should switch to show the structured client report with all 5 sections. Click through several clients. Stat cards should show 1 red, 2 yellow, and the average health score.

- [ ] **Step 6: Commit**

```bash
git add tools/status_reports.html
git commit -m "Render report selector, client reports, and leadership roll-up"
```

---

### Task 4: Copy Markdown Feature

**Files:**
- Modify: `tools/status_reports.html`

Implement the `copyMarkdown()` function that converts the current report to clean markdown and copies to clipboard.

- [ ] **Step 1: Write copyMarkdown()**

```javascript
function copyMarkdown() {
  let md = '';
  if (selectedReport === 'rollup') {
    md = generateRollupMarkdown();
  } else {
    const client = CLIENTS.find(c => c.id === selectedReport);
    if (client) md = generateClientMarkdown(client);
  }
  navigator.clipboard.writeText(md).then(() => {
    const btn = document.querySelector('.copy-btn');
    btn.textContent = 'Copied!';
    btn.classList.add('copied');
    setTimeout(() => { btn.textContent = 'Copy Markdown'; btn.classList.remove('copied'); }, 2000);
  });
}

function generateClientMarkdown(client) {
  let md = `# ${client.name} — Weekly Status Report\n\n`;
  md += `${REPORT_DATE} · Account Director: ${client.accountDirector} · Health: ${client.health} ${STATUS_EMOJI[client.status]}\n\n`;
  md += '## Progress\n\n';
  client.progress.forEach(item => { md += `- ${item}\n`; });
  md += '\n## Blockers\n\n';
  client.blockers.forEach(item => { md += `- ${item}\n`; });
  md += '\n## Next Steps\n\n';
  client.nextSteps.forEach(item => { md += `- ${item}\n`; });
  md += '\n## Budget Health\n\n';
  md += '| Metric | SOW | Actual | Status |\n|--------|-----|--------|--------|\n';
  const b = client.budget;
  md += `| Monthly Hours | ${b.sowHours} | ${b.actualHours} | ${b.utilization}% |\n`;
  md += `| Monthly Budget | ${formatMoney(b.monthlyBudget)} | ${formatMoney(b.actualCost)} | ${b.budgetStatus} |\n`;
  md += '\n## Key Metrics (March 2026)\n\n';
  if (client.metricsNote) md += `*${client.metricsNote}*\n\n`;
  md += '| Metric | Value | MoM Change |\n|--------|-------|------------|\n';
  client.metrics.forEach(m => { md += `| ${m.name} | ${m.value} | ${m.change} |\n`; });
  return md;
}

function generateRollupMarkdown() {
  let md = `# Monday Leadership Roll-Up\n\n`;
  md += `${ROLLUP.date} · Prepared for: ${ROLLUP.preparedFor}\n\n`;
  md += '## Accounts Requiring Attention\n\n';
  ROLLUP.attentionClients.forEach(ac => {
    md += `### ${ac.name} — ${ac.health} ${STATUS_EMOJI[ac.status]}\n\n`;
    ac.highlights.forEach(h => { md += `- ${h}\n`; });
    md += '\n';
  });
  md += '## Accounts On Track\n\n';
  md += '| Client | Health | Retainer | Status Note |\n|--------|--------|----------|-------------|\n';
  ROLLUP.onTrackClients.forEach(c => { md += `| ${c.name} | ${c.health} 🟢 | ${formatMoney(c.retainer)}/mo | ${c.note} |\n`; });
  md += '\n## Portfolio Summary\n\n';
  const ps = ROLLUP.portfolioSummary;
  md += '| Metric | Value |\n|--------|-------|\n';
  md += `| Total Monthly Retainer Revenue | ${formatMoney(ps.totalRevenue)} |\n`;
  md += `| Average Health Score | ${ps.avgHealth} |\n`;
  md += `| Blended Utilization | ${ps.blendedUtilization}% |\n`;
  md += `| Account Status | ${ps.greenCount} 🟢 / ${ps.yellowCount} 🟡 / ${ps.redCount} 🔴 |\n`;
  md += `| Accounts Over SOW Hours | ${ps.overSOW.join(', ')} |\n`;
  return md;
}
```

- [ ] **Step 2: Add .copied CSS class**

Add to style block:

```css
.copy-btn.copied { background: #28a745; }
```

- [ ] **Step 3: Verify copy works**

Open the page, click "Copy Markdown" on any report. Paste into a text editor. Expected: Clean markdown with `#` headings, `- ` bullets, `| ` pipe tables. No HTML tags.

- [ ] **Step 4: Commit**

```bash
git add tools/status_reports.html
git commit -m "Add copy-to-markdown feature for all reports"
```

---

### Task 5: Polish and Final Verification

**Files:**
- Modify: `tools/status_reports.html`

- [ ] **Step 1: Verify end-to-end**

Open `tools/status_reports.html`. Walk through:

1. Header shows "Sidebar Creative — Status Reports" with today's date
2. Stats show: 9 reports, 1 red, 2 yellow, avg health ~74
3. Roll-up is selected by default — shows Vello (red), Kova (yellow), Moonridge (yellow) in attention section
4. Green clients listed in compact table below
5. Portfolio summary shows $220K total revenue, 103% utilization
6. Click each of the 8 client reports — all render with 5 sections
7. Vello report shows critical blockers and declining metrics (all red)
8. Petal report shows seasonal normalization note on metrics
9. Copy Markdown on any report — paste verifies clean markdown
10. Copy Markdown on roll-up — verifies clean markdown with tables

- [ ] **Step 2: Commit**

```bash
git add tools/status_reports.html
git commit -m "Polish status report generator: final verification"
```

---

### Task 6: Process Log, Tutorial, and Final Commit

**Files:**
- Modify: `docs/process_log.md`
- Create: `tutorials/04_status_reports.md`

- [ ] **Step 1: Prepend a log entry to docs/process_log.md**

Add at the top (after YAML header), newest first:

```markdown
## 2026-04-08 — Status Report Generator (Tool 4)

**Prompt:** Build an automated status report generator for Sidebar Creative. Read all project data — timelines, hours burned vs. budgeted, milestones, RAG status. Generate a written weekly status report for each client covering progress, blockers, next steps, and budget health. Also generate an internal roll-up report summarizing all 8 clients for the Monday leadership meeting. Output the reports as markdown files and build an HTML interface to generate and view them.

**Built:** Interactive status report viewer with pre-authored reports for all 8 retainer clients plus a Monday leadership roll-up, with copy-to-markdown functionality.

**Key Decisions:**
- Single HTML file that generates and displays reports (no separate markdown files on disk)
- Structured template format: Progress, Blockers, Next Steps, Budget Health, Key Metrics
- Issues-first roll-up format (red/yellow clients first, green in compact table)
- Pre-authored report content (not AI-generated at runtime) for natural, context-specific language
- Week of March 31 – April 4 as the report period (latest complete data)
- Copy Markdown button for pasting into Slack, Google Docs, Notion

**Files Created/Modified:**
- `tools/status_reports.html` (created)
- `docs/superpowers/specs/2026-04-08-status-report-generator-design.md` (created)
- `docs/superpowers/plans/2026-04-08-status-report-generator.md` (created)
- `tutorials/04_status_reports.md` (created)
- `docs/process_log.md` (modified)
```

- [ ] **Step 2: Create the tutorial**

Create `tutorials/04_status_reports.md` with YAML header and step-by-step guide covering:
- The exact prompts used during brainstorming
- Key decisions: structured template, issues-first roll-up, pre-authored content, copy-to-markdown
- How report data was derived from the 4 data sources
- How the copy-to-markdown conversion works
- The final output file path: `tools/status_reports.html`

- [ ] **Step 3: Commit**

```bash
git add docs/process_log.md tutorials/04_status_reports.md
git commit -m "Add process log and tutorial for status report generator"
```
