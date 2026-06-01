# DonorEze — Product Requirements

This file captures the high-level product requirements for the DonorEze platform. It is read by Claude Code alongside CLAUDE.md to inform feature development, architecture decisions, and prioritization.

## Quick Summary — Read This First

**What it is:** Infrastructure layer for the giving economy — the "pipes," not a fundraising tool. Two apps on a shared Supabase backend: **Universal Vault™** (donor-facing, `donorli-donor-app`) and **ImpactOS™** (org-facing, `donorli_org_update`). Deployed on Vercel.

**Hard architectural constraint:** DonorEze does not hold, custody, or transmit funds. Money moves through Stripe. DonorEze is the intelligence, compliance, and connection layer only.

**Revenue:** Orgs pay transaction fees and (later) module subscriptions. Donors pay nothing, ever.

**Stage:** Pre-launch. MVP targets 10 small orgs and first real donation. Beta targets 100 small-to-medium orgs and first revenue toward a funding round.

**MVP scope:** Card payments only. No crypto, stock, DAF, or multi-asset settlement. No adjacencies (grants, volunteers, events).

**Central flywheel (org side):** TrustScore → SmartMatch ranking → Donor flow → Stewardship → TrustScore

**Donor "aha":** Interests matched to resonant orgs; seamless multi-org giving. **Org "aha":** Manual accounting/CRM/reporting load cut; matched donor targeting with minimal effort.

**Pending inputs:** Core problem statement (John), success metric reactions (John), org eligibility policy (John), Git branching convention (Dexter), donor-side vision document (Walt).

---

## Product Vision

DonorEze is the first **Infrastructure-as-a-Service (IaaS) provider for the giving economy** — building the "pipes" of philanthropy, not just fundraising tools. The platform aims to become the universal system of record for all philanthropic capital, regardless of asset type (cash, crypto, stock, DAF).

The core architecture is a **bi-directional DataBridge** connecting the donor's **Universal Vault™** (individual giving interface) with the organization's **ImpactOS™** (org management platform), eliminating manual data entry on both sides.

**Stage:** Pre-launch / MVP

**Revenue model:** Transaction fee paid by organizations per donation. Future: subscription tiers for organizations gating access to advanced features (analytics, AI agents, CRM integrations). Donors pay nothing — the platform is a free, premium financial tool for them. The model monetizes financial utility and compliance, not donor generosity.

---

## User Classes

|User|App|Description|
|---|---|---|
|**Donor**|donorli-donor-app (Universal Vault™)|Individual managing and giving across asset classes|
|**Organization**|donorli_org_update (ImpactOS™)|Nonprofits receiving donations, managing operations and compliance|
|**Admin**|donorli-admin (future)|Internal DonorEze team managing the platform|

### Target Donors

The platform serves donors at all levels — from casual/anonymous givers to habitual philanthropists to corporate donors to very high net worth individuals. **MVP focus: habitual philanthropists at all giving levels** — people who are serious about and motivated by their giving, regardless of dollar amount.

### Target Organizations

Covers the full spectrum from local nonprofits and community clubs (e.g. town Lions Clubs with only $100s–$1,000s) up to very large national nonprofits. **MVP/Beta will be primarily onboarded by smaller organizations** and grow up-market from there. The Strategic Summary is written for a $25M–$100M org — that is the long-term target, not the beachhead.

---

## Donor App Requirements (Universal Vault™)

*  **Authentication:** Guest checkout supported — donors can give without an account, with optional account creation after
*  **Multi-asset giving:** Long-term vision is cash, stock, and crypto — **MVP and Beta are card payments only**. Stock and crypto giving are post-Beta scope.
*  **Fund types:** Both evergreen funds and time-limited campaigns (with goals, deadlines, crisis/urgent states)
*  **Tax Center:** One-tap generation of a consolidated, audit-ready PDF of all gifts made across all organizations for the tax year
*  **ImpactStory™ feed:** Real-time, AI-curated feedback showing donors exactly how their specific contributions were used — creates an emotional feedback loop
*  **SmartMatch™ Engine:** Surfaces highest-impact org suggestions to donors. Ranking inputs include: org TrustScore, StewardshipInteractionScore (SIS), OrgImpactFactor, donor affinity, giving velocity, and real-time need signals (e.g. disaster triggers temporarily boost org weighting). Early version may exist in codebase — locate and assess before rebuilding.
*  **ImpactMultiplier™:** Visualizes the network effect of a gift (e.g. how a corporate gift triggers employee matches and social momentum)
*  **AI assistant scope:** Natural language intent parsing in the Vault; help donors discover orgs, understand impact, answer questions about orgs, and initiate donations through conversation

---

## Org App Requirements (ImpactOS™)

ImpactOS™ is structured as seven stackable modules. Each module added increases org switching cost and reduces churn. The central flywheel is:

**TrustScore → SmartMatch ranking → Donor flow → Stewardship → TrustScore**

### Module 1 — Core Infrastructure (ImpactOS™)

The foundation. All other modules depend on this.

*  **Institutional verification:** Org enters EIN; system auto-pulls last 3 Form 990s, public audit records, and state filings. AI parses program expense ratio, admin ratio, executive compensation, related-party disclosures, debt structure, revenue diversification.
*  **TrustScore (baseline):** `BaselineTrustScore = FTS + AIS + PER_SCORE + DCS + CRS`. Score is publicly visible to donors. Uploading third-party audit raises score — immediate incentive established.
*  **DataBridge:** Bi-directional sync with Salesforce, Blackbaud, accounting systems, and existing payment gateways. Every DonorEze gift auto-populates CRM with no manual entry. Eliminates 3–7 day reconciliation lag.
*  **Fund management:** Both evergreen funds and time-limited campaigns (with goals, deadlines, crisis/urgent states)
*  **ImpactStory™ feed:** Social feed for orgs to publish visual impact updates — "Instagram for Nonprofits"

### Module 2 — Advancement Services

Automates donor stewardship and retention.

* AI identifies donor segments: new donor, major donor, recurring, lapsed, disaster-response
* Prompts org staff with specific stewardship actions (thank-you within 4 hours, attach program photo, send quarterly update)
*  **StewardshipInteractionScore (SIS):** Each stewardship action increments SIS → feeds TrustScore (recalculated nightly) → affects SmartMatch ranking → affects donor flow. Creates a self-reinforcing capital loop.

### Module 3 — Impact Engine

Quantifies program output for donor-visible results.

* Org inputs: cost per beneficiary, deployment time, outcome metrics, geographic reach, program capacity
* System computes: `OrgImpactFactor = (ProgramEfficiency × 0.4) + (DeploymentVelocity × 0.3) + (OutcomeClarity × 0.2) + (BeneficiaryScale × 0.1)`
* OrgImpactFactor feeds donor ImpactScore directly
* Incentivizes orgs to quantify and publish impact — transparency becomes strategic advantage

### Module 4 — Compliance & AuditShield

Institutional-grade financial integrity.

* Third-party audit upload: +20% weighting boost to AIS component of TrustScore
* No audit: TrustScore penalty
* Real-time AI compliance monitoring: scans expense ratios, executive compensation spikes, related-party disclosures, late filings — flags anomalies
* Once donors rely on TrustScore, orgs cannot leave without losing credibility

### Module 5 — Intelligence / AI Layer

Predictive donor behavior and fundraising timing.

*  **Donor likelihood model:** `DonationProbability_30days = f(MissionOverlap, LiquiditySignals, EngagementHistory, EventTriggers)` — org dashboard shows "12 donors with 75%+ probability of giving in next 30 days"
*  **Capital flow forecasting:** Sector-level inflow trends, seasonal giving heatmap, mission category momentum (e.g. "Healthcare nonprofits seeing 18% increase this quarter")
*  **Disaster trigger:** AI detects disaster events → eligible orgs notified → activating emergency program boosts TrustScore visibility and SmartMatch weighting temporarily

### Module 6 — Transaction & Settlement Rails

Multi-asset acceptance and automated liquidation. *(Post-MVP — card only for MVP/Beta)*

* Accept: stock, crypto, DAF grants, cross-border wire — without separate systems
* Automated liquidation pipeline: asset verified → liquidated → proceeds deposited → compliance record stored → donor receipt generated → CRM updated → settlement timestamp logged
* <!-- website: which brokerage APIs and DAF custodians are targeted first? -->

### Module 7 — Data & Institutional Reporting

Board-level reporting and benchmarking.

* One-click report generation: board packet, impact summary, grant compliance report, donor segmentation analysis
* Board packet includes: TrustScore trendline, donor acquisition velocity, engagement ratio, ImpactScore distribution, peer benchmark percentile, program efficiency comparison
*  **Peer benchmarking:** Org sees percentile ranking vs peers on stewardship, deployment speed, efficiency — gamifies operational excellence
* Better metrics → Higher TrustScore → More donor flow

### Pricing Model (for context)

Three org tiers by revenue size: Tier A ($1M–$5M), Tier B ($5M–$25M), Tier C ($25M–$250M). Each module is separately priced. Transaction rails run at ~1% blended take rate. Full-stack annual revenue per org:~ $86.5k (A), ~$270k (B),~ $1.09M (C). See Strategic Summary doc for full pricing table.

---

## Cross-Cutting Requirements

*  **API-first design:** Platform is built as modern API-first infrastructure, enabling headless integration into any financial or philanthropic system
*  **Ubiquitous AI provisioning:** Every user and organization gets a dedicated, embedded AI agent — AI is integrated at every touchpoint, not bolted on
*  **Data-centric architecture:** Internal and third-party data treated with equal priority as a foundational logic layer, not just a reporting output
*  **PhilanthroGraph:** Maps connections between donors, corporations, and causes to identify "Impact Influencers" — individuals who move the needle for entire sectors
*  **Predictive philanthropy:** System should predict donor giving intent (when a donor is likely to liquidate assets or shift focus) so orgs can act proactively
*  **Integration ecosystem:** Long-term target of 1,000+ third-party application integrations — philanthropic insights flow across an org's entire tech stack
*  **B2B intelligence:** Corporate partner data layer providing insights on employee engagement and CSR ROI
*  **Multi-asset liquidity:** Ability to liquidate complex assets (stock, crypto) and settle into an org's operating currency in real time

---

## Future — Admin App Requirements

> To be specified when admin rebuild is initiated. The new admin will need to surface platform-level data across the PhilanthroGraph, manage compliance, and provide B2B intelligence dashboards.

---

## What Success Looks Like

### MVP

* Up to 10 small organizations onboarded and actively using the platform
* At least one real donation processed end-to-end

### Beta

* Up to 100 small to medium-sized organizations onboarded
* Revenue is non-zero — first transaction fees collected
* Engagement sufficient to support a formal funding round pitch

### For a Donor — the "aha" moment

* Their giving interests are visibly matched to organizations that align with their values
* The act of giving across multiple organizations is seamless — no friction, no confusion

### For an Org — the "aha" moment

* Manual accounting, CRM entry, and reporting workload meaningfully reduced
* Access to donor matching: potential donors whose values align with the org's mission are surfaced and campaigns can target them with minimal manual effort

---

## Open Questions

* What org subscription tiers will look like and what features they gate
* <!-- website: DAF custodian and stock brokerage integration targets — which providers are prioritized first? Decision pending. -->
* SmartMatch™ Engine — an early version may exist in the codebase; needs to be located and assessed before further development

---

## What DonorEze Will NOT Do

*  **No donor fees:** Donors pay nothing. Ever. All platform fees are borne by organizations.
*  **No direct fund management:** DonorEze does not hold, custody, or transmit funds directly. Actual money movement is handled by third-party payment processors (Stripe, etc.). DonorEze is the intelligence and rails layer, not a money transmitter. This is a hard architectural and legal boundary.
*  **No spam:** Communications to donors and orgs must be intentional, relevant, and minimal.
*  **No adjacencies at MVP/Beta:** Grant-making, volunteer coordination, event ticketing, and similar are future possibilities — well after Beta. Do not design for them prematurely.
*  **No cuts from donors:** Revenue comes from organizations only — transaction fees and subscriptions paid by orgs.

## Out of Scope (MVP/Beta)

* Grant-making
* Volunteer coordination
* Event ticketing
* Crypto and stock giving (card only for MVP/Beta)
* DAF and brokerage integrations
* Multi-asset liquidation and settlement
