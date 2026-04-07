---
title: Project Timeline Dashboard — Design Spec
version: 1.0
created: 2026-04-07
status: draft
---

# Project Timeline Dashboard

## Purpose

An interactive HTML dashboard that gives Sidebar Creative's project managers a Gantt-style timeline view of all active engagements — 8 retainer clients and 4 project-based engagements. PMs can scan status at a glance and click into any engagement for budget, milestone, team, and health detail.

## Design Decisions

- **Layout: Timeline + Detail Panel** — compact Gantt on top for scanning, click-to-expand detail panel below for depth. Chosen over single-page scroll (too tall) and modal-based (disruptive).
- **Visual style: Branded header, practical body** — Sidebar Creative identity in the header bar only. Everything below is clean, minimal, data-focused. PMs want practical, not pretty.
- **Retainer representation: Current SOW term** — each retainer shows its active contract period with renewal dates marked. Most useful for PM planning.
- **Progress tracking: Budget + Milestones** — progress bars show budget/hours burn. Milestone markers overlaid on timeline bars. PMs manage to both.
- **Single HTML file** — self-contained, no build step, no dependencies beyond what's inlined.

## Structure

### 1. Branded Header
- Dark background bar with "Sidebar Creative" name/wordmark
- Page title: "Project Timeline Dashboard"
- Current date display

### 2. Summary Stats Row
- Total active engagements (12)
- At-risk count (red + yellow clients)
- Overall budget health (% of engagements on track)
- Next upcoming deadline

### 3. Gantt Timeline

Two sections, visually separated with section headers:

**Retainer Clients (8 rows):**

| Client | SOW Start | SOW End | Monthly Retainer | Health | Notes |
|--------|-----------|---------|-----------------|--------|-------|
| Evergreen Outdoor Co. | Mar 2024 | Feb 2027 | $45,000 | 🟢 Green | Largest client |
| Brightwash Home | Jun 2024 | May 2027 | $38,000 | 🟢 Green | |
| Vello Cycling | Jan 2025 | Month-to-month | $32,000 | 🔴 Red | Churn risk |
| Petal & Thorn | Sep 2024 | Aug 2027 | $28,000 | 🟢 Green | |
| Kova Skincare | Nov 2024 | Oct 2025 (auto-renewed) | $25,000 | 🟡 Yellow | ROI concerns |
| Drift Coffee Roasters | Apr 2024 | Mar 2027 | $22,000 | 🟢 Green | |
| Summit Pet Supply | Aug 2025 | Jul 2026 | $18,000 | 🟢 Green | Expansion likely |
| Moonridge Spirits | Dec 2025 | Nov 2026 | $12,000 | 🟡 Yellow | Over-scope |

**Project Engagements (4 rows):**

| Project | Client | Start | End | Budget | Burned | Status |
|---------|--------|-------|-----|--------|--------|--------|
| River District Rebrand | River District BID | Jan 2026 | Apr 2026 | $85,000 | 73% | On track |
| Cascade Athletic Website | Cascade Athletic Wear | Feb 2026 | Jun 2026 | $120,000 | 40% | On track |
| Fieldwork Provisions Launch | Fieldwork Provisions | Mar 2026 | Jul 2026 | $95,000 | 19% | Early stage |
| NW Natural Rebrand | NW Natural Foods Co-op | Feb 2026 | Apr 2026 | $55,000 | 75% | Over scope |

**Timeline rendering:**
- Time axis: monthly columns from Jan 2026 – Dec 2026 (retainers may extend before/after — bars clip to visible window with arrows indicating continuation)
- Horizontal bar per engagement, colored by RAG status (green/yellow/red)
- Progress fill inside each bar showing budget burn %
- Diamond markers for milestones overlaid on bars
- Vertical red "today" line at April 7, 2026
- Renewal date markers (distinct icon) on retainer bars at SOW end dates
- Left column: client name + RAG status dot

**Interactions:**
- Click any bar or row to populate the detail panel below
- Hover for tooltip with quick summary (client, budget, health score)
- Filter toggles at top: All | Retainers | Projects

### 4. Detail Panel

Appears below the timeline when a row is clicked. Contains:

**Header:** Client/project name, overall health score, RAG badge

**Budget Section:**
- Retainers: monthly retainer amount, allocated hours, actual hours, utilization %, effective rate
- Projects: total budget, amount burned, remaining, burn rate trajectory

**Milestones Section:**
- List of key milestones with target dates and completion status (done/in-progress/upcoming)
- Retainer milestones: SOW renewal, QBR dates, seasonal campaigns
- Project milestones: phase completions, key deliverables, launch dates

**Team Section:**
- Assigned team members with name and role
- Sourced from brand definition primary team assignments

**Risk/Notes Section:**
- Key risk notes from brand definition (e.g., Vello's churn risk, Moonridge's scope issues)
- Displayed only when relevant notes exist

### 5. Milestone Data

Milestones are derived from the brand definition and SOW data:

**Retainer milestones (examples):**
- Evergreen: Q2 QBR, Holiday campaign planning (Q3), SOW renewal (Feb 2027)
- Vello: Renewal decision (at risk), Recovery plan checkpoint
- Petal & Thorn: Mother's Day campaign (Apr), Holiday prep (Sep)
- Kova: Quarterly review / possible downsize (Jul 2026)
- Summit: Scope expansion discussion (H2 2026)
- Moonridge: Brand identity phase completion (Mar 2026 — past), SOW review (Nov 2026)

**Project milestones:**
- River District Rebrand: Discovery complete, Concepts presented, Guidelines delivered (Apr 2026)
- Cascade Athletic: Design phase, Dev sprint 1, Dev sprint 2, QA, Launch (Jun 2026)
- Fieldwork Provisions: Brand phase, Web design, Web dev, Launch campaign (Jul 2026)
- NW Natural Rebrand: Concepts, Refinement, Final delivery (Apr 2026)

## Technical Approach

- Single self-contained HTML file with inlined CSS and JavaScript
- No external dependencies — all data hardcoded from brand definition
- CSS Grid for the Gantt layout
- Vanilla JavaScript for interactions (click handlers, filter toggles, detail panel)
- Responsive: functional at 1200px+ viewport width (dashboard use case)

## Out of Scope

- Live data connections (GA4, Harvest, etc.) — this is a static dashboard from current data
- Editing or updating data from the UI
- Mobile optimization (this is a desktop PM tool)
- Print/export functionality
