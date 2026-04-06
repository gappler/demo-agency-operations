---
title: Client Health Scoring Framework
version: 1.0
created: 2026-04-06
updated: 2026-04-06
parent: brand/brand_definition.md
---

# Client Health Scoring Framework

A composite scoring system for evaluating client account health across three dimensions. Designed to replace gut-feel assessments with data-driven signals that can be tracked monthly and surfaced in dashboards.

---

## Scoring Overview

Each client receives a composite health score (0–100) calculated from three weighted dimensions:

| Dimension | Weight | What It Measures |
|-----------|--------|-----------------|
| **Performance** | 40% | Are we delivering results against the client's KPIs? |
| **Profitability** | 35% | Is the account generating positive margin after actual hours? |
| **Relationship** | 25% | Is the client relationship healthy and stable? |

**Composite Score = (Performance × 0.40) + (Profitability × 0.35) + (Relationship × 0.25)**

### Status Thresholds

| Score Range | Status | Meaning | Action Required |
|-------------|--------|---------|----------------|
| 80–100 | 🟢 **Green** | Healthy account, on track | Standard management |
| 60–79 | 🟡 **Yellow** | Watch list, some concerns | Account review within 2 weeks |
| 0–59 | 🔴 **Red** | At risk, intervention needed | Partner-level escalation within 48 hours |

---

## Dimension 1: Performance (40% weight)

Measures whether Sidebar is delivering measurable results against the client's stated KPIs.

### Scoring Criteria

| Score | Criteria |
|-------|---------|
| 90–100 | Exceeding all primary KPIs by 10%+; metrics trending upward for 3+ months |
| 80–89 | Meeting all primary KPIs; most metrics stable or improving |
| 70–79 | Meeting most KPIs; 1–2 metrics flat or slightly below target |
| 60–69 | Missing 1–2 primary KPIs; metrics flat for 2+ months |
| 50–59 | Missing multiple KPIs; 1+ metrics declining for 2+ months |
| 40–49 | Significant KPI misses; declining trends across multiple metrics |
| 0–39 | Failing to meet most KPIs; consistent decline; client has raised concerns |

### Data Sources

- Google Analytics 4 (traffic, engagement, conversion)
- Meta Business Suite / LinkedIn / TikTok (social metrics)
- Klaviyo / Mailchimp (email performance)
- HubSpot (lead generation, pipeline)
- Client-defined KPI targets from SOW

### Signals That Lower Score

- 2+ consecutive months of declining traffic or leads
- Conversion rate below industry benchmark for category
- Social engagement rate dropping while follower count is flat
- Email open/click rates declining (list fatigue signal)
- Campaign ROAS below client's stated target
- Missed deliverable deadlines

---

## Dimension 2: Profitability (35% weight)

Measures whether the account is financially healthy for Sidebar after accounting for actual hours worked.

### Scoring Criteria

| Score | Criteria |
|-------|---------|
| 90–100 | Hours at or under SOW; effective rate above blended target ($165+/hr); consistent margin |
| 80–89 | Hours within 5% of SOW; effective rate at or near target |
| 70–79 | Hours 5–10% over SOW; effective rate slightly below target ($150–164/hr) |
| 60–69 | Hours 10–15% over SOW; effective rate $140–149/hr; margin thin but positive |
| 50–59 | Hours 15–20% over SOW; effective rate below $140/hr; account breaking even |
| 40–49 | Hours 20%+ over SOW; effective rate below $130/hr; account likely unprofitable |
| 0–39 | Significant and persistent overruns; account is a net cost; no change orders in place |

### Data Sources

- Harvest (actual hours logged)
- SOW hour allocations
- Monthly retainer fee
- Change order log (if any)

### Calculation

**Effective hourly rate = Monthly retainer ÷ Actual hours delivered**

| Effective Rate | Signal |
|---------------|--------|
| > $175/hr | Strong margin |
| $165–$175/hr | Target margin |
| $150–$164/hr | Acceptable, watch |
| $140–$149/hr | Thin margin |
| $130–$139/hr | Breaking even |
| < $130/hr | Unprofitable |

### Signals That Lower Score

- Hours over SOW for 2+ consecutive months without a change order
- Effective rate declining month-over-month
- Scope creep pattern: frequent small requests that individually seem minor but accumulate
- Senior resources (higher internal cost) doing work that should be handled by junior staff
- Rework or revision cycles exceeding contracted limits

---

## Dimension 3: Relationship (25% weight)

Measures the qualitative health of the client relationship, including satisfaction, communication quality, and expansion/contraction signals.

### Scoring Criteria

| Score | Criteria |
|-------|---------|
| 90–100 | Client actively advocates for Sidebar; expanding scope; exec-level engagement |
| 80–89 | Strong relationship; responsive; positive feedback; stable or growing scope |
| 70–79 | Good working relationship; occasional friction but resolved quickly |
| 60–69 | Relationship stable but transactional; limited strategic engagement |
| 50–59 | Signs of dissatisfaction; slower response times; questioning value |
| 40–49 | Active concerns raised; escalations; requesting competitive proposals |
| 0–39 | Relationship breakdown; non-responsive; termination discussions underway |

### Signal Indicators

**Positive signals (raise score):**
- Client refers Sidebar to other companies
- Scope expansion discussions initiated by client
- Client attends QBRs with senior leadership
- Fast approval turnarounds (<48 hours)
- Proactive communication about upcoming needs
- Client shares internal strategic plans with Sidebar team

**Negative signals (lower score):**
- Approval turnarounds exceeding 5 business days
- Key stakeholder turnover (new CMO/VP Marketing)
- Requesting competitive agency proposals
- Reduced meeting frequency or cancelled QBRs
- Feedback becoming more directive/less collaborative
- Delayed invoice payments (>30 days past due)
- Scope reduction discussions

### Data Sources

- Account team qualitative assessment (monthly)
- Meeting frequency and attendance tracking
- Email/Slack response time patterns
- NPS or satisfaction survey (quarterly)
- HubSpot activity log

---

## Current Client Health Scores (March 2026)

### 1. Evergreen Outdoor Co. — 🟢 91

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 92 | Exceeding KPIs; strong holiday season; traffic and leads up YoY |
| Profitability | 85 | Hours well-managed post-retainer expansion; effective rate $179/hr |
| Relationship | 95 | CMO is an advocate; expanded retainer in Jan; co-presents with Maya at conferences |
| **Composite** | **91** | |

### 2. Brightwash Home — 🟢 78

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 80 | Meeting most KPIs; subscription launch was a win; steady metrics |
| Profitability | 68 | Persistent 5–7% hour overruns; effective rate $158/hr; small requests not tracked |
| Relationship | 88 | Strong working relationship; client responsive; stable scope |
| **Composite** | **78** | |

**Watch item:** Profitability is the weak spot. The account looks healthy from performance and relationship angles, but is quietly eroding margin through untracked small requests. Needs scope discipline or a retainer adjustment.

### 3. Vello Cycling — 🔴 44

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 45 | Declining across all metrics for 5 consecutive months; missing KPIs |
| Profitability | 52 | 15–23% over SOW hours; effective rate $149/hr; rework driving overruns |
| Relationship | 35 | New VP Marketing hostile; competitive review underway; missed deadlines damaged trust |
| **Composite** | **44** | |

**Escalation:** Derek Okafor (partner) has joined all client calls. 60-day recovery plan in progress. Key actions: (1) present recovery strategy to VP Marketing, (2) deliver a "quick win" campaign in April, (3) renegotiate SOW terms if relationship stabilizes.

### 4. Petal & Thorn — 🟢 86

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 85 | Strong seasonal campaigns; Valentine's was record-breaking; off-peak metrics steady |
| Profitability | 90 | Hours consistently under SOW; effective rate $173/hr; seasonal surge well-managed |
| Relationship | 82 | Good relationship; client trusts the team; low-maintenance between peaks |
| **Composite** | **86** | |

### 5. Kova Skincare — 🟡 64

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 55 | Web redesign hasn't moved the needle; traffic flat; lead gen below milestones |
| Profitability | 70 | Slight hour overruns; effective rate $161/hr; acceptable but not strong |
| Relationship | 72 | Client likes the team but questioning ROI; VP Growth getting impatient |
| **Composite** | **64** | |

**Watch item:** If conversion metrics don't improve by May, the client will likely downsize the retainer at the July review. Strategy team needs to present a revised CRO plan with measurable 60-day milestones.

### 6. Drift Coffee Roasters — 🟢 83

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 78 | Modest growth; subscription platform performing well; steady metrics |
| Profitability | 88 | Hours consistently under SOW; effective rate $169/hr; efficient account |
| Relationship | 85 | Long-term, low-drama relationship; mutual trust; stable scope |
| **Composite** | **83** | |

### 7. Summit Pet Supply — 🟢 84

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 82 | Strong upward trajectory; metrics improving month-over-month |
| Profitability | 80 | Hours well-managed; effective rate $167/hr; room for growth |
| Relationship | 90 | Enthusiastic client; responsive; has asked about scope expansion twice |
| **Composite** | **84** | |

**Opportunity:** Client is signaling readiness to expand. Account team should prepare a $25K–$30K retainer proposal for H2 2026 that captures their growing content and paid media needs.

### 8. Moonridge Spirits — 🟡 60

| Dimension | Score | Notes |
|-----------|-------|-------|
| Performance | 65 | Metrics growing from a low base; brand identity work is strong but not yet measurable |
| Profitability | 48 | Averaging 14% over SOW; effective rate $136/hr; likely unprofitable in 2 of 4 months |
| Relationship | 70 | Founding team is passionate and engaged, but indecisive; revision cycles are 2x contracted |
| **Composite** | **60** | |

**Watch item:** This account needs either a retainer increase to $15K–$18K to cover actual hours, or stricter scope management with enforced revision limits. Current path is unsustainable.

---

## Portfolio Health Summary

| Status | Count | Clients | Revenue at Risk |
|--------|-------|---------|----------------|
| 🟢 Green | 5 | Evergreen, Brightwash, Petal & Thorn, Drift Coffee, Summit Pet | — |
| 🟡 Yellow | 2 | Kova Skincare, Moonridge Spirits | $37,000/month ($444K annual) |
| 🔴 Red | 1 | Vello Cycling | $32,000/month ($384K annual) |

**Total revenue at risk: $69,000/month ($828K annualized) — 31.4% of retainer revenue**

---

## Recommended Review Cadence

| Activity | Frequency | Owner | Participants |
|----------|-----------|-------|-------------|
| Health score calculation | Monthly | Ops Director | Account team inputs, Ops compiles |
| Green account review | Monthly | Account Manager | AM + PM |
| Yellow account review | Bi-weekly | Account Director | AD + PM + relevant discipline lead |
| Red account escalation | Weekly | Partner | Partner + AD + PM + strategy lead |
| Portfolio health review | Monthly | Managing Partner | All partners + Ops Director |
| QBR with client | Quarterly | Account Director | AD + strategy + discipline leads |

---

## Automation Opportunities

The current health scoring process is entirely manual — account directors fill in a spreadsheet based on judgment and memory. The reporting objective is to automate as much of this as possible:

1. **Performance dimension (70% automatable):** Pull KPI data directly from GA4, Meta, Klaviyo, HubSpot APIs. Auto-calculate trends and benchmark comparisons.

2. **Profitability dimension (90% automatable):** Pull hours from Harvest, compare against SOW allocations, calculate effective rates. Flag accounts trending toward unprofitability.

3. **Relationship dimension (30% automatable):** Response time patterns from email/Slack can be tracked. NPS scores from quarterly surveys can be auto-ingested. But the qualitative assessment (expansion signals, stakeholder sentiment) still requires human input.

**Target state:** A dashboard that auto-populates performance and profitability scores, prompts account directors for relationship inputs once per month, and calculates composite scores with trend indicators.
