---
description: "/campaign — 90-day GTM campaign planner + launch playbook. Produces full campaign brief with ICP mapping, channel mix, week-by-week timeline, KPIs, and orchestrates /social + /copy for asset production."
allowed-tools: Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, Skill, WebFetch
---

# /campaign — 90-Day GTM Campaign Planner + Launch Playbook

**Steel Principle: No campaign ships without an approved ICP, a defined motion (ABM / PLG / Hybrid), measurable KPIs, and explicit pivot rules. Planning first, assets second.**

> **When to use /campaign:**
> - Planning a product or feature launch
> - Building a 90-day pipeline push
> - Coordinating a multi-channel outbound sequence
> - Mapping a full campaign from ICP to asset manifest
>
> **When NOT to use /campaign:**
> - You just need a LinkedIn post - use `/social` instead
> - You just need copy for one email - use `/copy` instead
> - You need to build the brand/ICP context from scratch - run `/marketing` first
> - You need a prospect list - run `/prospect` first

---

## DISCIPLINE


> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)
Key enforcements for this skill:

- **Steel Principle #1:** No campaign plan proceeds without `icp.json` on disk. If it is missing, stop immediately and say "Run /prospect first to build your ICP file."
- **Steel Principle #2:** KPIs must be measurable numbers, not adjectives. "Increase brand awareness" is blocked. "Book 12 demos in 90 days" is approved.
- **Steel Principle #3:** Every channel in the mix must be justifiable with one sentence linking ICP behavior to that channel. Convention is not evidence.
- **Steel Principle #4:** Every campaign plan requires at least two pivot rules - written in advance - so you are not stuck defending a failing motion at week 8.

### Campaign-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "We can set KPIs later once we see how it goes" | Unmeasured campaigns always feel fine until they obviously fail | Set numeric targets before week 1 starts |
| "Let's run everything - email, LinkedIn, ads, events" | Spreading thin beats nothing at everything | Pick 2-3 primary channels and own them completely |
| "Our ACV is $50k but we should do PLG" | PLG requires self-serve volume; ABM requires precision. Match motion to ACV | ACV > $10k = ABM; ACV < $500 = PLG; in between = Hybrid |
| "90 days is too rigid - let's keep it flexible" | Flexible = no deadlines = no accountability | Locked week-by-week milestones with owners |
| "We'll produce assets as we go" | Assets produced mid-sequence cause gaps and silent dropoff | Full asset manifest approved before week 1 starts |
| "Skip the research - I know my ICP" | Undocumented ICP assumptions create misaligned copy and wrong channels | Write it down; validate against icp.json or create it |

---

## STATUS UPDATES

```
📣 /campaign Started
   Goal:     [stated goal]
   Segment:  [tier / segment]
   Timeline: [campaign window]

🎙️  Phase 0: Socratic Interview
   └─ 5 questions answered

📂 Phase 1: Context Load + Research
   ├─ brand.json loaded
   ├─ icp.json loaded
   └─ Research notes saved

📋 Phase 2: Plan + HARD GATE
   ├─ Campaign brief drafted
   ├─ Channel mix scored
   ├─ Week-by-week timeline built
   ├─ KPI framework set
   ├─ Asset manifest listed
   ├─ Pivot rules defined
   └─ ✅ Plan approved

⚙️  Phase 3: Execute
   ├─ campaign.json written
   └─ [sub-skills chained if requested]

🔍 Phase 4: Verification
   └─ All milestones, channels, KPIs, pivot rules checked

✅ Campaign Ready
   Reports saved to: .marketing-reports/YYYYMMDD-HHMMSS-campaign/
```

---

## SESSION FOLDER SETUP

```bash
mkdir -p .marketing-reports
grep -q ".marketing-reports" .gitignore 2>/dev/null || echo ".marketing-reports/" >> .gitignore
SESSION_DIR=".marketing-reports/$(date +%Y%m%d-%H%M%S)-campaign"
mkdir -p "$SESSION_DIR"
```

All outputs for this run go in `$SESSION_DIR`. Reference this path in every write operation.

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Reports and artifacts live in gitignored `.campaign-reports/` directories
- Any temporary files or sessions are closed at the end of the run
- No persistent resources are left behind unless explicitly requested

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these five questions. All five must be answered before proceeding. If the user answers multiple in one message, accept them and note which remain.

1. "What's the goal? Awareness / pipeline / activation / retention / upsell?"
2. "Which segment and roughly how many accounts? Tier A / B / all?"
3. "Timeline and hard deadlines? (campaign window in weeks, any immovable dates)"
4. "Which channels can you actually execute across? (realistic, not wishful)"
5. "What would 'success' look like specifically in 90 days? (revenue, pipeline, demos, MQLs, etc.)"

Write answers to `$SESSION_DIR/interview-answers.md` when complete.

---

## PHASE 1: CONTEXT LOAD + RESEARCH

### 1.1 Load Brand Context

Check in this order:
1. `.marketing-context/brand.json` in the project directory - if found, use it
2. `~/.claude/marketing-global/brand-*.json` - if found, list and offer to pick
3. If neither exists, say "No brand.json found. Run /marketing first to establish brand context."

### 1.2 Load ICP (Required)

Check for `.marketing-context/icp.json`. If missing, STOP and say:

```
ICP file not found at .marketing-context/icp.json.
Run /prospect first to build your ICP file, then return to /campaign.
```

Extract from icp.json:
- Primary tier (Tier A or B)
- Firmographics (company size, revenue, industry)
- Trigger events
- Buying committee roles
- Behavioral signals

### 1.3 Optional Research

Offer: "Want me to research current campaign patterns working in your category this quarter? (This uses WebFetch and adds ~2 minutes)"

If accepted: research 2-3 relevant sources, extract what is working (hooks, channels, cadence), and write findings to `$SESSION_DIR/research-notes.md`.

If declined: note "Research skipped per user preference" in research-notes.md and continue.

---

## PHASE 2: PLAN + HARD GATE

Write all sections below to `$SESSION_DIR/plan.md`. Update this file live as the user gives feedback - it is the single source of truth.

### 2.1 Campaign Brief

```markdown
## Campaign Brief

**Campaign Name:** [descriptive + memorable]
**Slug:** [kebab-case, used for file naming]
**Goal Statement:** [one sentence, verb + measurable outcome + timeframe]
**Target Segment:** [from icp.json - Tier A / B / specific criteria]

**Campaign Motion:**
- ABM (Account-Based Marketing): Use when ACV > $10k, defined target list, multi-person campaigns per account
- PLG (Product-Led Growth): Use when ACV < $500, self-serve funnel, community + content heavy
- Hybrid: Use when ACV $500-$10k, blending inbound content with targeted outbound

**Campaign Motion Selected:** [ABM / PLG / Hybrid] — Rationale: [one sentence]

**90-Day Window:**
- Start date: [YYYY-MM-DD]
- End date: [YYYY-MM-DD]
- Hard deadlines: [list any immovable dates - product releases, events, fiscal quarters]

**Budget Guidance:**
- Bootstrap ($0-$500/mo): Organic only - LinkedIn, email to owned list, content
- Growth ($500-$5k/mo): Add paid LinkedIn or cold email tooling
- Scale ($5k+/mo): Full paid stack + retargeting
- Selected tier: [based on user input or default to Bootstrap if unspecified]
```

### 2.2 Channel Mix Recommendation

Score every channel the user mentioned (from interview question 4) plus any others that fit the ICP. Use the 3-axis scoring model below.

**Scoring:**
- Audience Fit: Is the ICP actually on/in this channel? (1 = weak, 2 = moderate, 3 = strong)
- Budget Fit: Does this channel work at the stated budget tier? (1-3)
- Timeline Fit: Can this channel deliver within the campaign window? (1-3)

Include only channels scoring 7+. For any channel below 7, state why it was excluded.

| Channel | Audience Fit | Budget Fit | Timeline Fit | Total | Include? |
|---------|-------------|------------|--------------|-------|---------|
| LinkedIn organic | ? | ? | ? | ? | ? |
| X organic | ? | ? | ? | ? | ? |
| Cold email (outbound) | ? | ? | ? | ? | ? |
| Newsletter / Beehiiv | ? | ? | ? | ? | ? |
| Content marketing (blog + SEO) | ? | ? | ? | ? | ? |
| Paid LinkedIn ads | ? | ? | ? | ? | ? |
| Webinars | ? | ? | ? | ? | ? |
| Community engagement | ? | ? | ? | ? | ? |
| Referrals | ? | ? | ? | ? | ? |

**Primary Channels (2-3):** [list selected channels with rationale per channel]
**Supporting Channels (1-2):** [amplifiers - lower effort, reinforce primary]

### 2.3 30/60/90-Day Sprint Structure

Prefer three 30-day sprints over a monolithic 90-day plan. Sprints allow course correction without abandoning the campaign.

**Sprint 1 (Days 1-30): Build + Seed**
- Prospect list finalized (link to /prospect if needed)
- LinkedIn profile warm-up (connection requests, engagement, no cold pitches)
- Outbound sequences loaded and validated
- Content foundation: 4 posts queued, 1 core piece (guide, essay, or case study) published

**Sprint 2 (Days 31-60): Activate + Amplify**
- Outbound sequence live - first replies coming in
- Content cadence active - 2-3x/week
- Nurture running for non-responders
- Weekly gate metrics reviewed every Friday

**Sprint 3 (Days 61-90): Close + Convert**
- Direct ask emails to warm leads
- Demo push - personal outreach to everyone who opened 3+ emails
- Case study or proof published
- Retrospective data collected for next campaign

### 2.4 Week-by-Week Timeline

Build a concrete week-by-week milestone table. Customize based on the campaign motion and goal.

**ABM Default:**

| Week | Focus | Key Activities | Owner | Gate Metric |
|------|-------|----------------|-------|-------------|
| 1-2 | Foundation | Finalize account list (Tier A), LinkedIn warm-up, load sequences | [user/agent] | List locked, sequences verified |
| 3-4 | Seed | First outbound touches, content week 1 published | [user/agent] | 50+ LinkedIn connection requests sent |
| 5-6 | Activate | Follow-up sequence live, 2nd content piece | [user/agent] | 10+ replies or engagement signals |
| 7-8 | Amplify | Webinar or live event invite, nurture active | [user/agent] | 5+ demos booked |
| 9-10 | Nurture | Case study published, referral ask to happy users | [user/agent] | Pipeline value $X |
| 11-12 | Close | Direct ask emails, CEO personal note to Tier A non-responders | [user/agent] | Demo + close push active |
| 13 | Retro | Data collection, win/loss notes, pivot or extend decision | [user/agent] | All KPIs measured |

**PLG Default:**

| Week | Focus | Key Activities | Owner | Gate Metric |
|------|-------|----------------|-------|-------------|
| 1-2 | Funnel Audit | Review onboarding flow, identify dropoff, fix top 3 gaps | [user/agent] | Funnel map complete |
| 3-4 | Content Seed | Publish 2 SEO-targeted pieces, 4 social posts | [user/agent] | Content live, indexed |
| 5-8 | Community | Active in 2 communities, answer 3+ threads/week | [user/agent] | 20+ community interactions |
| 9-10 | Amplify | Referral program or in-product expansion trigger | [user/agent] | Referral loop active |
| 11-13 | Convert | Email to trialists, direct upgrade ask, webinar for advanced users | [user/agent] | Upgrade rate measured |

**Launch Playbook Template (for product/feature launches):**

```
Pre-launch (T-30): teaser content, waitlist or beta invite page, warm up email list
Pre-launch (T-14): beta user testimonials, behind-the-scenes content
Pre-launch (T-7): countdown content, reminder email, SDR outreach to Tier A
Launch Week:      coordinated drop - email blast, LinkedIn posts, PR if applicable
Post-launch (D+7):  follow-up to non-openers, case study from beta users
Post-launch (D+30): nurture sequence live, paid amplification if budget allows
Post-launch (D+60): second case study, retargeting ads, referral ask
```

### 2.5 KPI Framework

Every KPI must have: a metric name, a target number, a measurement method, and a review cadence.

```markdown
## KPI Framework

**Primary Goal KPI:**
- Metric: [demos booked / MQLs / pipeline value / activations / upsells]
- Target: [number]
- Method: [HubSpot pipeline / PostHog events / manual count]
- Review: Weekly on Fridays

**Funnel KPIs:**
| Stage | Metric | Target | Formula |
|-------|--------|--------|---------|
| Outreach | Emails sent | [X/week] | - |
| Outreach | Open rate | >40% | Opens / Delivered |
| Outreach | Reply rate | >8% | Replies / Delivered |
| Pipeline | Demo booked rate | >25% of replies | Demos / Positive replies |
| Pipeline | Demo-to-close rate | >20% | Closed / Demos |
| Revenue | Pipeline value | $[X] | Deals x ACV |

**Unit Economics KPIs:**
- Target CAC: $[X] (total campaign spend / new customers)
- LTV:CAC ratio target: [3:1 minimum]
- Payback period: [X months]

**Weekly Gate Metrics (reviewed every Friday):**
| Week | Must Hit To Continue |
|------|---------------------|
| 4 | 50+ outreach sends, >10 replies |
| 8 | 5+ demos booked, 1 pipeline opp |
| 12 | [Primary goal] >= 50% of target |
```

### 2.6 Asset Manifest

List every asset the campaign requires. Each asset gets an ID for cross-referencing with touchpoints in campaign.json.

```markdown
## Asset Manifest

### Email Sequence (invoke /copy for each)
| Asset ID | Asset | Status | /copy Task |
|----------|-------|--------|-----------|
| email_001 | Outreach email 1 - Pattern interrupt | pending | /copy --type=cold-email --persona=champion |
| email_002 | Outreach email 2 - Case study proof | pending | /copy --type=cold-email --persona=champion |
| email_003 | Outreach email 3 - Direct ask | pending | /copy --type=cold-email --persona=champion |
| email_004 | Nurture email - Value add | pending | /copy --type=nurture |
| email_005 | Demo follow-up | pending | /copy --type=follow-up |

### LinkedIn Posts (invoke /social for each)
| Asset ID | Asset | Status | /social Task |
|----------|-------|--------|-------------|
| social_001 | Week 1 - Problem post | pending | /social --platform=linkedin --angle=problem |
| social_002 | Week 2 - Insight post | pending | /social --platform=linkedin --angle=insight |
| social_003 | Week 3 - Case study post | pending | /social --platform=linkedin --angle=proof |
| social_004 | Week 4 - Direct CTA post | pending | /social --platform=linkedin --angle=cta |

### Landing Page (invoke /copy)
| Asset ID | Asset | Status | /copy Task |
|----------|-------|--------|-----------|
| lp_001 | Demo request page | pending | /copy --type=landing-page |

### Sales Enablement
| Asset ID | Asset | Status |
|----------|-------|--------|
| sales_001 | One-pager / leave-behind | pending |
| sales_002 | Objection handling guide | pending |

### Supporting Content
| Asset ID | Asset | Status |
|----------|-------|--------|
| content_001 | Core piece (guide / essay) | pending |
| content_002 | Case study template | pending |
```

### 2.7 Pivot Rules

Written in advance. Prevents the "stick with a failing campaign for 90 days" trap. Explicit thresholds, not vague feelings.

```markdown
## Pivot Rules

| Rule | Trigger Condition | Action |
|------|-------------------|--------|
| P1 - Channel pivot | Email open rate < 20% by week 4 | Switch primary channel to LinkedIn DM; pause email sequence |
| P2 - Message pivot | Reply rate < 3% after 100+ sends | Rewrite sequence angles; invoke /copy with alt framework |
| P3 - Segment pivot | Zero Tier A replies by week 6 | Expand to Tier B; reduce ACV threshold for target accounts |
| P4 - Motion pivot | ABM demos < 3 by week 8 | Blend in PLG: publish self-serve trial page, add content cadence |
| P5 - Kill switch | No demos booked by week 10 | Full review meeting; consider campaign pause + ICP re-validation |
```

### 2.8 HARD GATE

Present the plan summary and REQUIRE explicit approval before proceeding to Phase 3.

```
═══════════════════════════════════════════════════════════════
CAMPAIGN PLAN REVIEW
═══════════════════════════════════════════════════════════════

  Campaign:   [campaign name]
  Goal:       [goal statement]
  Segment:    [ICP tier + criteria]
  Motion:     [ABM / PLG / Hybrid]
  Timeline:   [start] - [end]
  Channels:   [primary 1], [primary 2], [supporting 1]
  Assets:     [N] emails, [N] posts, [N] landing pages
  Top KPI:    [primary KPI with target]
  Pivot Rule: [P1 trigger listed]

  Does this accurately reflect your campaign intent?
  Approve to proceed to execution.
═══════════════════════════════════════════════════════════════
```

Do NOT proceed to Phase 3 until the user explicitly approves.

---

## PHASE 3: EXECUTE

### Option A: Generate campaign.json and Stop

Write structured JSON output for agent/human consumption to `$SESSION_DIR/campaign.json`:

```json
{
  "campaign_name": "",
  "slug": "",
  "goal": "",
  "target_segment": "icp_tier_a",
  "motion": "ABM",
  "timeline_weeks": 13,
  "sprint_1_end_week": 4,
  "sprint_2_end_week": 8,
  "sprint_3_end_week": 13,
  "touchpoints": [
    {
      "week": 1,
      "channel": "linkedin",
      "asset_id": "social_001",
      "asset_type": "post",
      "status": "pending",
      "owner": "user",
      "scheduled": "YYYY-MM-DDTHH:MM:SS-05:00"
    },
    {
      "week": 2,
      "channel": "email",
      "asset_id": "email_001",
      "asset_type": "cold-email",
      "status": "pending",
      "owner": "user",
      "scheduled": "YYYY-MM-DDTHH:MM:SS-05:00"
    }
  ],
  "kpis": {
    "primary_metric": "",
    "primary_target": 0,
    "target_demos_booked": 0,
    "target_pipeline_value": 0,
    "target_cac": 0,
    "ltv_cac_ratio_target": "3:1",
    "weekly_gate_metrics": [
      { "week": 4, "metric": "email_replies", "threshold": 10 },
      { "week": 8, "metric": "demos_booked", "threshold": 5 },
      { "week": 12, "metric": "primary_goal_pct", "threshold": 50 }
    ]
  },
  "pivot_rules": [
    {
      "id": "P1",
      "trigger": "email open rate < 20% by week 4",
      "action": "switch to LinkedIn DM; pause email sequence"
    }
  ],
  "assets": [],
  "created": "",
  "last_updated": ""
}
```

Note: all `scheduled` timestamps use Central Time (CT) - either `-06:00` (CST) or `-05:00` (CDT).

### Option B: Full Asset Production (user must explicitly request)

If user says "produce all assets" or "chain to /copy and /social":

1. For each email in the asset manifest, invoke `/copy` sub-agent with:
   - The approved campaign brief
   - The ICP buying committee role (champion / economic buyer / etc.)
   - The messaging framework from brand.json
   - The specific asset ID and type

2. For each social post in the asset manifest, invoke `/social` sub-agent with:
   - The approved campaign brief
   - The platform (LinkedIn / X)
   - The content angle (problem / insight / proof / CTA)
   - The week number for scheduling context

3. Write all produced assets to `$SESSION_DIR/assets/` organized by type:
   ```
   $SESSION_DIR/assets/
     emails/
       email_001.md
       email_002.md
     social/
       social_001.md
       social_002.md
     landing-pages/
       lp_001.md
   ```

4. Update `asset-manifest.md` marking each produced asset as complete.

---

## PHASE 4: VERIFICATION

Re-read `plan.md` and `campaign.json`. Check every item below. Mark each PASS or FAIL.

```markdown
## Verification Checklist

### Milestones
- [ ] Every week in the timeline has a named activity (not "TBD")
- [ ] Every milestone has an owner (user / agent / TBD is not acceptable past week 2)
- [ ] Every milestone has a gate metric

### Channels
- [ ] Every selected channel has a rationale sentence linking ICP behavior to channel
- [ ] Every channel has at least one asset assigned in the manifest
- [ ] No channel was added by convention alone (eliminated if no ICP evidence)

### KPIs
- [ ] Primary KPI is a number, not an adjective
- [ ] Every KPI has a measurement method
- [ ] Weekly gate metrics exist for weeks 4, 8, and 12
- [ ] CAC and LTV:CAC targets set

### Pivot Rules
- [ ] At least 2 pivot rules defined
- [ ] Each rule has a concrete numeric threshold (not "if performance is weak")
- [ ] Each rule has a specific action, not "revisit strategy"

### Assets
- [ ] Every touchpoint in campaign.json maps to an asset ID
- [ ] Every asset ID in campaign.json appears in the asset manifest
- [ ] All scheduled timestamps use CT timezone (-05:00 or -06:00)
```

Write verification results to `$SESSION_DIR/verification.md`. If any item FAILS, fix it before finalizing.

---

## PHASE 5: SITREP

## SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

Write to `$SESSION_DIR/sitrep.md` AND display on screen.

```
═══════════════════════════════════════════════════════════════
SITREP — /campaign
═══════════════════════════════════════════════════════════════
Project:  [project name]  |  Branch: [git branch]
Started:  [HH:MM CT]      |  Completed: [HH:MM CT]
Status:   [COMPLETE / PARTIAL / BLOCKED]
───────────────────────────────────────────────────────────────

CAMPAIGN
  Name:     [campaign name]
  Goal:     [goal statement]
  Segment:  [ICP tier + criteria]
  Motion:   [ABM / PLG / Hybrid]
  Timeline: [start date] - [end date] ([N] weeks)

CHANNELS
  Primary:   [channel 1], [channel 2]
  Support:   [channel 3]

ASSETS
  Total touchpoints: [N]
  Emails:            [N] ([N] produced / [N] pending)
  Social posts:      [N] ([N] produced / [N] pending)
  Landing pages:     [N] ([N] produced / [N] pending)

KPIS
  Primary:        [metric] >= [target]
  Demos target:   [N]
  Pipeline value: $[X]
  CAC target:     $[X]
  LTV:CAC:        [ratio]

PIVOT RULES
  [N] rules defined. First trigger: [P1 condition]

VERIFICATION
  Milestones: [PASS / FAIL - N items]
  Channels:   [PASS / FAIL]
  KPIs:       [PASS / FAIL]
  Pivot Rules:[PASS / FAIL]
  Assets:     [PASS / FAIL]

CLEANUP
  Session folder: .marketing-reports/[slug]/
  Gitignored:     yes

NEXT ACTION
  [If Option A] Share campaign.json with team or agent. Invoke /copy + /social to produce assets.
  [If Option B] All assets produced. Review in $SESSION_DIR/assets/. Load sequences into HubSpot.
───────────────────────────────────────────────────────────────
Report: $SESSION_DIR/sitrep.md
═══════════════════════════════════════════════════════════════
```

---

## WORKED EXAMPLE

**Scenario:** IronWorks - a B2B agent orchestration platform targeting VP Engineering and CTOs at 50-500 person SaaS companies. ACV $24k. Goal: 8 demos booked in 90 days.

**Phase 0 Answers:**
1. Goal: Pipeline - book demos with technical decision-makers
2. Segment: Tier A (50-500 employees, SaaS, recently hired CTO or VP Eng)
3. Timeline: 13 weeks, hard deadline at week 13 for Q2 board update
4. Channels: LinkedIn organic, cold email, webinar (one event)
5. Success: 8 demos booked, 2 closed/won, $48k new ARR

**Motion Selected:** ABM - ACV $24k, defined target list of 50 accounts, multi-person campaigns per account (CTO + VP Eng per account = 2 contacts)

**Channel Scoring:**
| Channel | Audience | Budget | Timeline | Total | Include? |
|---------|----------|--------|----------|-------|---------|
| LinkedIn organic | 3 (CTOs active) | 3 (Bootstrap) | 2 (slow build) | 8 | Yes - Primary |
| Cold email | 3 (direct outreach) | 3 (Bootstrap) | 3 (fast) | 9 | Yes - Primary |
| Webinar | 2 (maybe) | 2 (setup cost) | 2 (1 event) | 6 | Yes - Supporting (1 event only) |
| Paid LinkedIn | 2 (right audience) | 1 (Growth needed) | 3 (fast) | 6 | No - budget constraint |

**KPIs:**
- Primary: 8 demos booked by week 13
- Gate week 4: 10+ email replies
- Gate week 8: 4+ demos booked
- Gate week 12: 7+ demos booked
- CAC target: $3,000 (50 accounts x 2 contacts = 100 outreach; 8% reply = 8 replies; 50% demo rate = 4 demos minimum at sprint midpoint)

**Pivot Rules:**
- P1: If email open rate < 25% by week 4, rewrite subject lines using /copy with PAS framework
- P2: If reply rate < 5% after 80 sends, switch primary angle from pain-led to outcome-led
- P3: If 0 demos by week 7, add 25 Tier B accounts and reduce outreach to Tier A to 2 touches/account

**Campaign JSON touchpoint sample:**
```json
{ "week": 1, "channel": "linkedin", "asset_id": "social_001", "status": "pending", "scheduled": "2026-04-14T09:00:00-05:00" },
{ "week": 2, "channel": "email", "asset_id": "email_001", "status": "pending", "scheduled": "2026-04-21T08:00:00-05:00" }
```

**Asset count produced in this example:** 5 emails, 8 LinkedIn posts, 1 webinar invite, 1 landing page = 15 assets total.

---

## SESSION FOLDER STRUCTURE

```
.marketing-reports/YYYYMMDD-HHMMSS-campaign/
  interview-answers.md     - Phase 0 answers
  research-notes.md        - Phase 1 research findings (optional)
  plan.md                  - Live campaign brief (updated throughout)
  campaign-brief.md        - Final approved brief
  campaign.json            - Machine-readable campaign output
  asset-manifest.md        - All assets needed + status
  pivot-rules.md           - Explicit pivot conditions
  verification.md          - Phase 4 checklist results
  sitrep.md                - Final situation report
  assets/
    emails/
    social/
    landing-pages/
```

---

## RELATED SKILLS

**Feeds from:**
- `/marketing` - brand context + positioning framework (produces brand.json)
- `/prospect` - ICP file + account list (produces icp.json)

**Feeds into:**
- `/social` - campaign JSON + channel plan drives social content calendar
- `/copy` - asset manifest + ICP + messaging framework drives copy production

**Pairs with:**
- `/prospect` - run first when ABM; produces Tier A account list that populates touchpoints
- `/copy` - run per asset type after campaign.json approved
- `/social` - run for full social calendar once channel mix confirmed

**Suggested after /campaign:**
When campaign.json is approved, suggest: `/copy` for email sequence, then `/social` for LinkedIn calendar.

---

## APPENDIX: ATTRIBUTION MODELING

**When to use which attribution model based on deal size and sales cycle.**

Source: Mouseflow B2B SaaS attribution guide, ORM Tech marketing attribution analysis, Dreamdata attribution model documentation, The Clueless Company revenue attribution framework.

B2B SaaS buyer journeys average 211 days and 266 touchpoints across 5-10 decision-makers. Single-touch models miss most of the story.

### Model Comparison

| Model | What It Credits | Best For | Avoid When |
|-------|----------------|----------|------------|
| First-touch | 100% to the first interaction | ACV < $500, short sales cycle (PLG), understanding top-of-funnel effectiveness | ACV > $5k, multi-stakeholder deals |
| Last-touch | 100% to the final interaction before conversion | Quick-close promotions, single-session purchases | Any deal > 30-day cycle - systematically over-credits retargeting |
| Linear | Equal credit to every touchpoint | Early-stage companies wanting a baseline before more sophisticated models | Budget allocation decisions - too averaged to drive action |
| W-shaped | 30% to first touch, 30% to lead creation, 30% to opportunity creation, 10% split across remaining | ACV $5k-$50k, defined sales stages, clear CRM pipeline | Businesses without clear stage definitions in CRM |
| Data-driven | Algorithmically assigns credit based on actual influence on outcomes | ACV > $50k, 100+ closed-won deals in the dataset, mature analytics stack | Small datasets (under 100 closed deals) - the algorithm needs volume to be accurate |

### Decision Framework

```
What is your average ACV?
├── Under $500 (PLG, self-serve)
│   → Use first-touch. Understand what fills the funnel.
│   → Pair with: product analytics (activation rate, time-to-value)
│
├── $500 - $10k (Hybrid motion)
│   → Use W-shaped. You have multi-touch journeys but distinct stages.
│   → Requires: CRM with MQL, SQL, Opportunity stages tracked
│
├── $10k - $100k (ABM)
│   → Use W-shaped or Time-decay. Multiple stakeholders, long cycle.
│   → Consider: account-level attribution (not just lead-level)
│
└── Over $100k (Enterprise)
    → Use Data-driven or custom algorithmic attribution.
    → Requires: 100+ closed-won deals in dataset, dedicated BI/analytics resource
    → Tools: Dreamdata, Rockerbox, or custom GA4 + CRM integration
```

### Implementation Minimum Requirements

Before running any attribution model in reporting:

```markdown
## Attribution Readiness Check

- [ ] UTM parameters applied consistently to ALL paid and owned traffic sources
- [ ] CRM tracks deal stage transitions with timestamps (not just current stage)
- [ ] Closed-won and closed-lost are both tracked (not just wins)
- [ ] Marketing activities (emails, ads, content) are linked to contact records in CRM
- [ ] Multi-touch attribution requires: HubSpot Revenue Attribution, Salesforce Campaign Influence, or Dreamdata/Rockerbox

If UTMs are inconsistent: fix them before trusting any model.
If deal stages lack timestamps: fix CRM configuration before running W-shaped.
```

### Attribution Model by Sales Cycle Length

| Sales cycle | Recommended model |
|-------------|------------------|
| Under 2 weeks | First-touch (top-of-funnel insight) + Last-touch (what closed it) |
| 2-8 weeks | W-shaped (captures awareness, lead, close) |
| 2-6 months | W-shaped or Time-decay (recent touches weighted more) |
| Over 6 months | Data-driven only (complexity requires algorithmic weighting) |

---

## APPENDIX: BUDGET ALLOCATION FRAMEWORK

**The 70/20/10 rule with quarterly adjustment protocol.**

Source: Google/Coca-Cola 70/20/10 framework, Golden Owl Digital B2B 70/20/10 guide 2026, Data-Mania B2B Marketing Budget Benchmarks 2026.

### The Framework

```
70% - Proven channels (core)
  What it funds: Channels with demonstrated ROI, measurable pipeline contribution, repeatable results.
  B2B examples: Organic search, email to owned list, branded paid search, proven cold email sequences.
  Rule: Never experiment here. Scale what works.

20% - Emerging channels (test)
  What it funds: Channels with some signal but not yet proven in your specific context.
  B2B examples: LinkedIn paid (testing new audiences), new content formats, new outbound sequences.
  Rule: Structured tests with defined success metrics. 60-90 day window before judgment.

10% - Experimental (learn)
  What it funds: New channels, new messaging frameworks, new markets.
  B2B examples: TikTok/Reels B2B content, Threads, co-marketing pilots, podcast sponsorships.
  Rule: Expected failure rate is high. Measure learning, not just ROI.
```

### Quarterly Adjustment Protocol

Review every quarter. Do not wait for the annual plan cycle.

```markdown
## Quarterly Budget Review

**Review date:** [Date - schedule for first week of each new quarter]

**Performance assessment - 70% bucket:**
- Which core channels hit or exceeded target CAC? [list + confirm continued]
- Which missed by more than 20%? [list + flag for root cause analysis]
- Any channel in the 70% bucket that has been underperforming for 2+ consecutive quarters? [move to 20% or cut]

**Performance assessment - 20% bucket:**
- Which emerging channels graduated to proven? [move to 70%]
- Which ran their test window without hitting the success metric? [move to 10% or cut]
- Any new emerging channel to add? [what signal triggered consideration?]

**Performance assessment - 10% bucket:**
- What did we learn? [one finding per experiment, not just a ROI number]
- Which experiments failed fast enough to free budget for a new test? [reallocate]
- Any experiment that should move to 20%? [signal threshold: 1 closed deal or 3x attributed pipeline]

**Adjustments made:**
| Channel | Old Bucket | New Bucket | Reason |
|---------|-----------|-----------|--------|
| [Channel] | 70% | 20% | [Specific underperformance metric] |
```

### Budget Allocation by Stage

Adjust the 70/20/10 split based on company stage:

| Stage | Core (proven) | Testing (emerging) | Experimental |
|-------|--------------|-------------------|-------------|
| Pre-PMF | 40% | 40% | 20% |
| Post-PMF, pre-scale | 60% | 30% | 10% |
| Scaling (> $1M ARR) | 70% | 20% | 10% |
| Enterprise/mature | 80% | 15% | 5% |

Pre-PMF caveat: higher experimentation budget is justified because you have not yet found the channels that consistently produce qualified pipeline. Running 70% in "proven" when nothing is proven yet is capital misallocation.

---

## APPENDIX: LAUNCH PLAYBOOK (EXPANDED)

**Week-by-week detail for product and feature launches.**

The standard launch calendar in Phase 4 gives a framework. This appendix gives the actual activities, with owners and gates, for each week.

### Pre-Launch (T-8 to T-5 weeks)

| Week | Activity | Owner | Gate |
|------|----------|-------|------|
| T-8 | Write launch messaging document: core message, supporting messages, proof points. Share with sales. | Marketing | Messaging doc approved by founder + head of sales |
| T-8 | Identify beta users for testimonials and case study quotes. Outreach sent. | CS/Marketing | 3+ beta users confirmed |
| T-7 | Landing page copy written. Design brief to design team. | Copy/Design | Landing page brief approved |
| T-6 | Teaser content posted: problem posts (not product posts) on LinkedIn. Build curiosity, not anticipation. | Marketing | 4+ teaser posts published |
| T-5 | Beta user interviews conducted. Pull 2-3 specific quotes + one metric. | CS | Quotes approved by customers in writing |

### Pre-Launch Week (T-4 to T-2 weeks)

| Week | Activity | Owner | Gate |
|------|----------|-------|------|
| T-4 | Landing page live (waitlist or beta capture mode, not full launch). SEO indexing begins. | Eng/Marketing | Page indexed, UTMs working |
| T-4 | Email list segment built: who gets the launch email and in what order (VIP first, then full list) | Marketing/Ops | Segment verified in email platform |
| T-3 | PR outreach to 10-15 journalists who cover your space. Send the story, not a press release. (See PR module) | Founder/Marketing | 2-3 journalist replies (positive or requesting more info) |
| T-2 | Sales enablement: one-pager, objection guide, launch email templates distributed to sales team | Marketing | SDR team confirms receipt and readiness |
| T-2 | Final copy review across all launch assets. Check: every asset uses launch messaging doc. | Marketing | All assets checked against messaging doc |

### Launch Week

| Day | Activity | Owner | Gate |
|-----|----------|-------|------|
| Day 1 (Monday) | VIP customer email - personal, from founder. Short. One link. | Founder | Sent before 9am CT |
| Day 1 | LinkedIn announcement post - founder's personal account (not company page) | Founder | Published 8-9am CT |
| Day 2 | Full list launch email | Marketing | Deployed 8am CT |
| Day 2 | ProductHunt submission (if relevant for the product) | Marketing | Submitted before 12am CT (midnight) to capture full-day voting |
| Day 3 | Response email to non-openers of Day 2 email (different subject line, same offer) | Marketing | Scheduled for 8am CT |
| Day 3-4 | Twitter/X thread from founder: the story behind why this was built | Founder | Published, pinned to profile |
| Day 5 | Week 1 performance check: email open rate, landing page CVR, demos booked | Marketing | Data captured in campaign.json |

### Post-Launch (Weeks 2-8)

| Week | Activity | Owner | Gate |
|------|----------|-------|------|
| Week 2 | Follow-up to non-responders: different angle (outcome-led vs. initial pain-led) | Marketing | Segment = opened but did not convert |
| Week 3 | First case study from beta user published | Marketing/CS | Customer approved in writing |
| Week 4 | Retargeting ads live (if Growth/Scale budget tier) | Marketing | Pixel verified, audiences loaded |
| Week 5-6 | Second content piece: goes deeper on the problem the product solves | Marketing | Published and distributed via social |
| Week 8 | Retrospective: what worked, what did not, what changes for next launch | Marketing + Founder | Retro doc saved to session folder |

---

## APPENDIX: CO-MARKETING AND PARTNERSHIP PLAYBOOK

**How to find partners, what to trade, and how to measure.**

Source: Partner2B partner-led growth playbook, Baremetrics strategic partnerships guide, Channeltivity complete partner marketing guide 2025, Kalungi B2B SaaS partner strategy.

Research shows partner-sourced deals have 40% higher average order value, close 46% faster, and win 53% more often than non-partner deals. The ROI on a working partnership is significant - but most partnerships fail because they are set up without clear exchange value.

### Step 1: Identify Partner Candidates

Good co-marketing partners share an audience but do not compete for the same solution.

```markdown
## Partner Candidate Criteria

**Audience overlap:** They sell to the same ICP. Their customers have the same role, pain, and profile as yours.

**Non-competitive:** They solve a different part of the same problem. Your product and theirs are additive, not substitutable.

**Comparable credibility:** Their brand reputation in the market is neutral or positive for yours. Do not co-market with a brand that has active controversies in your market.

**Willing to invest:** A co-marketing partnership requires someone on their side to do work. If their response to "would you co-market?" is "sure, what do you need from us?" - they are not ready. A real partner asks "what are we both going to get out of this?"

**Discovery sources:**
- Integrations you already have (customers already use both products)
- "Tools used alongside us" from customer surveys
- LinkedIn job postings from your ICP - what else are they buying?
- Conferences where you both exhibit or sponsor
- G2/Capterra "frequently bought together" or "used with" sections
```

### Step 2: What to Trade

The most common co-marketing exchange formats, ranked by relationship depth:

| Format | What You Trade | Time to Value | Relationship Depth |
|--------|---------------|--------------|-------------------|
| Newsletter swap | One send to each other's lists | 1-2 weeks | Light - transactional |
| Joint webinar | Shared audience, shared speaker effort | 3-6 weeks | Medium |
| Co-authored guide or report | Both brands listed, shared distribution | 6-10 weeks | Medium-high |
| Joint GTM motion (integrated + co-sell) | Sales intros, bundled pricing, shared pipeline | 3-6 months | Deep |
| Integration partnership | Technical integration + co-marketing | 3-12 months | Deepest |

Start with a newsletter swap or joint webinar. These are low-risk ways to test whether the audience actually overlaps before investing in deeper work.

### Step 3: The Co-Marketing Agreement

Before executing any co-marketing, align on these five items in writing (email is sufficient):

```markdown
## Co-Marketing Alignment Doc

**Partners:** [Your company] + [Partner company]
**Format:** [Newsletter swap / joint webinar / joint guide / other]
**Topic:** [Specific topic - not "something about our integration"]
**What each party contributes:**
  - We contribute: [specific deliverable + timeline]
  - They contribute: [specific deliverable + timeline]
**What each party receives:**
  - We receive: [list reach, leads, attribution]
  - They receive: [list reach, leads, attribution]
**Lead ownership:** [Leads from joint assets - how are they split and who can market to them?]
**Promotion commitment:** [How many times will each party promote this asset? Which channels?]
**Go-live date:** [Date]
**Review date:** [30 days post-launch to assess results]
```

### Step 4: Measurement

Track these for every co-marketing activity:

```markdown
## Co-Marketing KPIs

**Top of funnel:**
- Joint asset views or registrations attributed to partner's promotion
- New contacts sourced from partner's audience (not already in your CRM)

**Pipeline:**
- Opportunities created where the partner is noted as the source
- Demos or discovery calls attributed to the partnership

**Revenue:**
- Closed-won deals where partner influence is logged
- Average deal size: partner-sourced vs. non-partner (should be higher per research above)

**Relationship health:**
- Partner follow-through rate: did they execute their commitments?
- Repeat partnership: did this produce a second co-marketing activation?

**Rule:** If a co-marketing activity produces zero new contacts after 60 days, it is not a marketing partnership - it is a favor exchange. Measure and decide whether to continue.
```

### Step 5: When Not to Partner

- When the "partnership" is one-sided (you create the content, they share once with minimal effort)
- When their audience does not actually overlap with your ICP (vanity reach is not pipeline)
- When the partner wants to lead-gate the joint asset and keep all leads
- When you are pre-PMF and cannot yet clearly articulate what your customers get from the integration

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — 90-day GTM campaign planner + launch playbook -->
<!-- Part of the Claude Code Skills Collection -->
<!-- License: MIT -->
