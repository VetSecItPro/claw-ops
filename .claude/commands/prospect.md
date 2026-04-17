---
description: "/prospect — ICP definition + prospect research + lead scoring. Writes icp.json and scoring.json shared with sibling skills. Supports batch scoring and individual prospect briefs."
allowed-tools: Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /prospect — ICP Definition, Prospect Research & Lead Scoring

**Steel Principle: No list leaves this skill without a defined ICP, explicit negative ICP, and a verified scoring model. Scoring first, outreach second.**

A full-stack prospect intelligence skill. Give it a context and a goal; it returns an ICP document, a scoring model, and either a prioritized prospect list or deep account briefs - all ready to hand to `/campaign` or `/copy`.

> **When to use /prospect:**
> - Building or refreshing your ICP for the first time
> - Scoring an inbound list or cold-built account CSV
> - Producing deep 1-10 account briefs before high-value outreach
> - Syncing lead data to HubSpot or Apollo
>
> **When NOT to use /prospect:**
> - You need copy for the outreach itself - use `/copy` instead
> - You need a full campaign plan - use `/campaign` instead
> - You need brand or positioning work - use `/marketing` instead

---

## DISCIPLINE


> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)
**Steel Principle #1:** No scoring output without an explicit negative ICP. Omitting disqualifiers is not the same as having none.

**Steel Principle #2:** ICP tiers must be defined by observable signals, not by gut feel. Every tier must specify at least one firmographic, one technographic, and one behavioral or trigger signal.

**Steel Principle #3:** MEDDPICC scores are for active opportunities only. Do not conflate prospect scoring with deal qualification.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "Our ICP is anyone who needs this" | Vague ICPs produce vague outreach that converts nobody | Define at minimum: industry, employee range, one must-have tech signal |
| "Skip the ICP workshop, we already know who to target" | Undocumented ICPs drift with every sales hire | Run the workshop anyway - it takes 15 minutes and prevents months of wasted pipeline |
| "A score of 40 is close enough, reach out anyway" | Tier C outreach pollutes sender reputation and wastes SDR cycles | Respect the thresholds; move Tier C to nurture only |
| "We'll define the negative ICP later" | Without disqualifiers, reps waste time on accounts that can never buy | Negative ICP is required before any list is exported |
| "Clay/Apollo will find the best accounts" | Tools surface data; judgment determines fit | Tools execute the ICP you define - garbage in, garbage out |
| "The trigger event is just a nice-to-have" | Prospects without a trigger event take 3-5x longer to close | Prioritize triggered accounts in every list, always |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

**Skill-specific status template:**

```markdown
🚀 /prospect Started
   Mode:     [ICP Workshop / Scoring / Brief / Batch]
   Target:   [N accounts / ICP refresh / batch CSV]
   Tools:    [Apollo / Clay / BuiltWith / Manual]

🔍 Phase 0: Socratic Interview
   └─ ✅ 5 answers captured

📁 Phase 1: Context Load
   ├─ Loaded brand.json
   ├─ Loaded existing icp.json (if present)
   └─ ✅ Context loaded

🎯 Phase 2: Plan + HARD GATE
   └─ ⏸ Awaiting user approval

🔨 Phase 3: Execute [mode]
   └─ ✅ [N prospects scored / ICP document written / N briefs produced]

🧪 Phase 4: Verification
   └─ ✅ 7/7 checks passed

📊 Phase 5: SITREP
   └─ ✅ Report written to .marketing-reports/[session]/sitrep.md
```

**Autonomy rule:** After the Phase 2 HARD GATE approval, the skill runs the selected mode end-to-end without further confirmations. Do not re-prompt the user between Phases 3-5.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Reports and artifacts live in gitignored `.prospect-reports/` directories
- Any temporary files or sessions are closed at the end of the run
- No persistent resources are left behind unless explicitly requested

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these five questions before doing any work. Wait for answers before proceeding.

**Q1:** "What is the trigger event or context for this research? (Examples: funding round, new CTO hire, cold list build from scratch, product launch, event follow-up)"

**Q2:** "Describe your best current customer in detail. What industry are they in, how large, what tools do they use, and what problem were they solving when they found you?"

**Q3:** "How many accounts do we need, and at what depth? Depth = 1-10 accounts with full research briefs. Breadth = 50-500 account list with scores."

**Q4:** "What tools are available for enrichment? (Apollo, Clay, LinkedIn Sales Navigator, BuiltWith, none - manual research)"

**Q5:** "What would make a prospect 'not a fit'? Name at least two specific disqualifiers."

Display a summary of answers and confirm before Phase 1.

---

## PHASE 1: CONTEXT LOAD

**Setup:**
```bash
mkdir -p .marketing-reports
mkdir -p .marketing-context
grep -q ".marketing-reports" .gitignore 2>/dev/null || echo ".marketing-reports/" >> .gitignore
grep -q ".marketing-context" .gitignore 2>/dev/null || echo ".marketing-context/" >> .gitignore
```

**Brand context load:**
- Check `.marketing-context/brand.json` - if found, load it for positioning context
- If not found, check `~/.claude/marketing-global/` for named brand files
- Note: brand context informs ICP language and proof points but does not gate execution

**Research offer:**
Ask: "Want me to research current prospecting sources, competitor customer bases, or industry signals beyond the baseline? (Uses WebFetch - adds 5-10 min)"

If accepted, use WebFetch to research:
- Target industry news and hiring patterns
- Competitor G2/Capterra reviews for ICP signals
- Job board patterns (LinkedIn, Indeed) indicating buying intent
- Any company URLs the user provides

Save all research findings to the session folder as `research-notes.md`.

---

## PHASE 2: PLAN + HARD GATE

Determine mode from the interview answers:

- **ICP Workshop Mode** - no icp.json exists, or user wants a refresh
- **Lead Scoring Mode** - icp.json exists, need scoring.json or a scored list
- **Prospect Brief Mode** - 1-10 accounts, deep research per account
- **Batch Scoring Mode** - CSV input, score and rank at scale

Write `plan.md` to the session folder with the appropriate section below.

### Plan: ICP Workshop Mode

- ICP workshop output: firmographics, technographics, behavioral signals, buying committee map
- Tier A / B / C definitions with observable signals for each
- Negative ICP with specific disqualifiers (not just "small companies")
- Trigger events list with 30-day action window

### Plan: Lead Scoring Mode

- Scoring dimensions and point weights to apply
- Positive and negative scoring rules
- Threshold tiers with prescribed follow-up action per tier
- Output format (JSON, CSV, or both)

### Plan: Prospect Brief Mode

- Research dimensions per account (8 dimensions listed in Phase 3)
- Personalization angle extraction approach
- Recommended sequence per account tier

### Plan: Batch Scoring Mode

- Source CSV structure expected
- Enrichment strategy: Apollo only / Apollo + Clay waterfall / manual BuiltWith lookup
- Scoring rubric to apply
- Output format and sort order

**HARD GATE:**

```
═══════════════════════════════════════════════════════════════
PLAN REVIEW — /prospect
═══════════════════════════════════════════════════════════════

  Mode:        [ICP Workshop / Scoring / Brief / Batch]
  Target:      [N accounts / ICP refresh / batch CSV]
  Tools:       [tools available]
  Output:      [icp.json / scoring.json / briefs / ranked CSV]

  Does this match what you need?
  Approve to proceed, or describe what to change.
═══════════════════════════════════════════════════════════════
```

Do not proceed until the user approves.

---

## PHASE 3: EXECUTE

### ICP Workshop Mode

Apply the ICP 2.0 template. Populate all fields using interview answers plus any research gathered.

#### ICP 2.0 Template (Full)

**Tier A Profile - Ideal Fit:**

**Firmographics:**
- NAICS codes: [2-3 specific codes, not broad categories]
- Industry: [specific vertical, not "technology" or "services"]
- Employee range: [e.g., 50-500]
- Revenue range: [e.g., $5M-$100M ARR]
- Funding stage: [e.g., Series A-C, or bootstrapped profitable]
- Geography: [specific regions or excluded regions]
- Business model: [SaaS / services / marketplace / hybrid]

**Technographics:**
- Must-have tools (strong fit signal): [e.g., HubSpot, Salesforce, Slack, AWS]
- Strong signals (high correlation): [e.g., Zapier, Outreach.io, Gong]
- Disqualifiers (tech that means bad fit): [e.g., Salesforce Classic only, on-prem everything]
- Source: BuiltWith lookup, Apollo technographic filters, Clay enrichment

**Behavioral Signals:**
- Content consumption: [e.g., follows G2 category, subscribes to industry newsletters]
- Hiring patterns: [e.g., posting for "Head of Revenue Operations"]
- Review site activity: [e.g., actively comparing competitors on G2]
- Event attendance: [e.g., attends SaaStr, RevOps conference]
- Public signals: [e.g., LinkedIn posts about manual process pain, budget approval delays]

**Trigger Events (30-day action window):**
1. New CTO or VP of Engineering hired (check LinkedIn "started new role")
2. Series A/B/C funding announced (Crunchbase, TechCrunch)
3. Job posting for [specific role indicating pain - e.g., "Revenue Operations Manager"]
4. Public complaint about current tool on LinkedIn or Twitter
5. Product launch or expansion announcement
6. Competitor contract renewal window (approx. 60 days before anniversary)

**Buying Committee Map:**

| Role | Titles | JTBD | Primary Fear | Proof Needed |
|------|--------|------|--------------|--------------|
| Economic Buyer | VP Engineering, CTO, COO | Allocate budget to highest-ROI initiative | Buying wrong tool, wasting capital | ROI case study with hard numbers |
| Champion | Director of Ops, RevOps Lead | Eliminate the manual work making their team look slow | Looking bad to their boss | Peer reference, demo that works |
| End User | SDR, AE, CS Manager | Get their job done without friction | Learning curve, disruption | Short onboarding timeline, UI demo |
| Blocker | IT Security, Legal, Finance | Protect the org from risk and budget waste | Surprise costs, compliance gaps | SOC 2, pricing transparency, SLA |

**Tier B Profile - Good Fit, Longer Cycle:**
- Same dimensions as Tier A, relaxed on 1-2 criteria
- Specify which criteria are relaxed and by how much
- Expected deal size vs Tier A, expected cycle length vs Tier A

**Tier C Profile - Nurture Only:**
- Fits some criteria but missing a critical Tier A/B signal
- Do not pursue with SDR time; route to automated nurture
- Quarterly review to check for tier upgrade signals

**Negative ICP (Do Not Target):**
These are explicit disqualifiers, not just "not Tier A":
- [e.g., Competitor employees: score -20, do not contact]
- [e.g., Under 20 employees: premature, no budget, score -15]
- [e.g., Student or .edu email: score -15, remove from sequences]
- [e.g., Government/public sector: procurement cycle incompatible]
- [e.g., Unsubscribed: score -10, CAN-SPAM/GDPR compliance]
- [e.g., Industry: [specific vertical] - regulatory constraints or margin profile]

**Win/Loss Validation (quarterly):**
Track by quarter:
- Win rate by tier (Tier A target: >40%, Tier B: >20%, Tier C: <10%)
- Average deal size by tier
- Average sales cycle length by tier
- Top 3 objections by tier
- Top 3 reasons lost by tier

Update icp.json with `last_validated` date each quarter.

**Write `.marketing-context/icp.json`:**

```json
{
  "version": "2.1",
  "last_validated": "[YYYY-MM-DD]",
  "created_by": "/prospect",
  "tier_a": {
    "label": "Ideal Fit",
    "firmographics": {
      "naics_codes": [],
      "industries": [],
      "employee_range": [50, 500],
      "revenue_range": ["$5M", "$100M"],
      "funding_stages": [],
      "geographies": [],
      "business_models": []
    },
    "technographics": {
      "must_have": [],
      "strong_signal": [],
      "disqualifier": []
    },
    "behavioral_signals": [],
    "trigger_events": [],
    "buying_committee": {
      "economic_buyer": {
        "titles": [],
        "jtbd": "",
        "primary_fear": "",
        "proof_needed": ""
      },
      "champion": {
        "titles": [],
        "jtbd": "",
        "primary_fear": "",
        "proof_needed": ""
      },
      "end_user": {
        "titles": [],
        "jtbd": "",
        "primary_fear": "",
        "proof_needed": ""
      },
      "blocker": {
        "titles": [],
        "jtbd": "",
        "primary_fear": "",
        "proof_needed": ""
      }
    }
  },
  "tier_b": {
    "label": "Good Fit",
    "relaxed_criteria": [],
    "firmographics": {},
    "technographics": {},
    "behavioral_signals": [],
    "trigger_events": []
  },
  "tier_c": {
    "label": "Nurture Only",
    "description": "",
    "review_cadence": "quarterly"
  },
  "negative_icp": [
    { "signal": "", "score_penalty": 0, "action": "remove" }
  ],
  "win_loss_data": {
    "last_updated": "",
    "tier_a_win_rate": 0,
    "tier_b_win_rate": 0,
    "tier_c_win_rate": 0
  }
}
```

Also write `icp-document.md` to the session folder as the human-readable version.

---

### Lead Scoring Mode

Apply the 100-point scoring model. Every prospect gets a total score and an assigned tier.

#### Scoring Rubric (100 Points Positive)

**Firmographic Fit (30 pts total):**
| Signal | Max Points | How to Verify |
|--------|-----------|---------------|
| Industry match (exact NAICS) | 10 | Apollo, LinkedIn company page |
| Employee count in range | 8 | Apollo, LinkedIn headcount |
| Revenue / funding stage in range | 7 | Crunchbase, Apollo revenue filter |
| Geography match | 5 | Apollo, LinkedIn HQ location |

**Technographic Fit (25 pts total):**
| Signal | Max Points | How to Verify |
|--------|-----------|---------------|
| Automation tools present (Zapier, n8n, Make) | 10 | BuiltWith, Apollo technographics |
| CRM in use (HubSpot, Salesforce) | 8 | BuiltWith, Apollo technographics |
| AI/LLM tools deployed | 7 | BuiltWith, job postings, LinkedIn |

**Behavioral Signals (25 pts total):**
| Signal | Max Points | How to Verify |
|--------|-----------|---------------|
| Pricing page visit | 10 | PostHog / GA4 behavioral events |
| Resource download (whitepaper, guide) | 8 | HubSpot form submission |
| 3 or more email opens in sequence | 5 | HubSpot/Instantly tracking |
| Case study page view | 2 | PostHog / GA4 |

**Intent Signals (20 pts total):**
| Signal | Max Points | How to Verify |
|--------|-----------|---------------|
| Trigger event occurred (see ICP list) | 10 | Google Alerts, LinkedIn, Crunchbase |
| G2 competitor category research | 6 | G2 Buyer Intent (paid) or G2 public reviews |
| Relevant job posting (target role) | 4 | LinkedIn Jobs, Indeed |

**Negative Scoring:**
| Signal | Penalty | Action |
|--------|---------|--------|
| Competitor employee detected | -20 | Remove from sequence |
| Under 20 employees (too small) | -15 | Move to Tier C / remove |
| Email unsubscribed | -10 | CAN-SPAM compliance - do not contact |
| Student or .edu email | -15 | Remove from list |
| Industry on negative ICP list | -25 | Remove entirely |

**Score Thresholds and Prescribed Actions:**

| Tier | Score Range | Action | Timeline |
|------|-------------|--------|----------|
| Tier A | 80-100 | Immediate SDR outreach + CEO personal note | Same day |
| Tier B | 50-79 | Automated nurture sequence + SDR follow-up | Day 14 |
| Tier C | 0-49 | Nurture only, no SDR time | Quarterly review |
| Disqualified | Score goes negative | Remove from list | Immediate |

**Write `.marketing-context/scoring.json`:**

```json
{
  "version": "1.0",
  "last_updated": "[YYYY-MM-DD]",
  "created_by": "/prospect",
  "max_positive_score": 100,
  "dimensions": {
    "firmographic_fit": {
      "max_points": 30,
      "signals": {
        "industry_match": 10,
        "employee_count": 8,
        "revenue_funding": 7,
        "geography": 5
      }
    },
    "technographic_fit": {
      "max_points": 25,
      "signals": {
        "automation_tools": 10,
        "crm_present": 8,
        "ai_llm_deployed": 7
      }
    },
    "behavioral_signals": {
      "max_points": 25,
      "signals": {
        "pricing_page_visit": 10,
        "resource_download": 8,
        "email_opens_3plus": 5,
        "case_study_view": 2
      }
    },
    "intent_signals": {
      "max_points": 20,
      "signals": {
        "trigger_event": 10,
        "g2_competitor_research": 6,
        "relevant_job_posting": 4
      }
    }
  },
  "negative_scoring": {
    "competitor_employee": -20,
    "too_small_under_20": -15,
    "unsubscribed": -10,
    "student_email": -15,
    "negative_icp_industry": -25
  },
  "thresholds": {
    "tier_a": { "min": 80, "max": 100, "action": "immediate_sdr_plus_ceo_note", "timeline_days": 0 },
    "tier_b": { "min": 50, "max": 79, "action": "nurture_plus_sdr_day14", "timeline_days": 14 },
    "tier_c": { "min": 0, "max": 49, "action": "nurture_only", "timeline_days": null },
    "disqualified": { "max": -1, "action": "remove", "timeline_days": 0 }
  }
}
```

---

### Prospect Brief Mode (1-10 Accounts)

For each account, research and document all 8 dimensions. Write one file per account to `prospect-briefs/<company-slug>.md`.

**8 Research Dimensions:**

1. **Company Snapshot**
   - Domain, HQ location, employee count, founding year
   - Funding stage and total raised (Crunchbase)
   - Business model and primary product/service
   - LinkedIn company URL, website

2. **ICP Tier and Score**
   - Calculated score using scoring rubric
   - Tier assignment (A/B/C)
   - Top 3 signals that drove the score
   - Any negative signals found

3. **Trigger Event**
   - Most recent trigger event (if any)
   - Date detected
   - Source (LinkedIn, Crunchbase, job board, Google Alerts)
   - 30-day window status (active/expired)

4. **Tech Stack Signals**
   - Tools detected via BuiltWith (free: 5/day limit)
   - CRM, automation, AI/LLM tools specifically
   - Any disqualifying tools found

5. **Buying Committee Mapping**
   Identify all 4 roles via LinkedIn People search where possible:
   - Economic Buyer: [name, title, LinkedIn URL if found]
   - Champion: [name, title, LinkedIn URL if found]
   - End User representative: [name, title, LinkedIn URL if found]
   - Blocker (IT/Legal/Finance): [name, title, LinkedIn URL if found]

6. **Recent News and Content**
   - Last 90 days: press releases, blog posts, LinkedIn company posts
   - Leadership posts on LinkedIn (champion and economic buyer)
   - Anything signaling strategic priority or pain

7. **Job Postings (Strategic Signals)**
   - Open roles that indicate buying intent
   - Roles that indicate the pain our solution addresses
   - Roles that indicate budget exists
   - Source: LinkedIn Jobs, Indeed, company careers page

8. **Personalization Angle**
   - Specific detail from research to use in first-touch outreach
   - Must be observable (not inferred), specific (not generic), recent (last 90 days)
   - Format: "I noticed [specific thing] - that suggests [inference]. That's exactly what we help with."
   - Recommended sequence: [which sequence template maps to this trigger/tier]

**Prospect Brief Template:**

```markdown
# [Company Name] - Prospect Brief

**Generated:** [date]
**ICP Tier:** [A/B/C]
**Score:** [N/100]
**Trigger Active:** [Yes - [event] on [date] / No]

---

## Company Snapshot
- **Domain:** [domain]
- **HQ:** [city, country]
- **Size:** [N employees]
- **Funding:** [stage, $amount, date]
- **Model:** [SaaS / Services / etc.]

## Score Breakdown
| Dimension | Score | Max | Key Signal |
|-----------|-------|-----|-----------|
| Firmographic | | 30 | |
| Technographic | | 25 | |
| Behavioral | | 25 | |
| Intent | | 20 | |
| Negative | | — | |
| **Total** | | **100** | |

## Trigger Event
[Description or "None detected"]

## Tech Stack
- CRM: [tool or unknown]
- Automation: [tools or unknown]
- AI/LLM: [tools or unknown]
- Source: BuiltWith / Apollo / Manual

## Buying Committee
| Role | Name | Title | LinkedIn | Confidence |
|------|------|-------|----------|-----------|
| Economic Buyer | | | | High/Med/Low |
| Champion | | | | High/Med/Low |
| End User | | | | High/Med/Low |
| Blocker | | | | High/Med/Low |

## Recent Signals
- [Date]: [signal]
- [Date]: [signal]

## Job Postings
- [Role] - [date posted] - [link]

## Personalization Angle
[Specific, recent, observable detail + inference + connection to value prop]

## Recommended Sequence
[Sequence name / SDR action / nurture only]
```

---

### Batch Scoring Mode (CSV Input)

1. Ask user to provide CSV with columns: `company_name, domain, employee_count, industry, [any additional known fields]`
2. For each row, apply available signals from CSV + any enrichment possible
3. If Apollo API key available: enrich firmographics and technographics
4. If Clay available: run waterfall enrichment (Apollo -> Clearbit -> LinkedIn)
5. Apply scoring rubric to each row
6. Flag accounts where critical data is missing (score marked as `partial`)
7. Sort output by score descending
8. Write `prospect-scores.json` to session folder

**Output format per account:**
```json
{
  "company": "",
  "domain": "",
  "icp_tier": "A",
  "score": 0,
  "score_breakdown": {},
  "trigger_event": "",
  "missing_data": [],
  "recommended_action": ""
}
```

---

## PHASE 4: VERIFICATION

Before writing final outputs, verify:

- [ ] ICP covers all 5 dimensions: firmographic, technographic, behavioral, intent, buying committee
- [ ] Scoring model adds to exactly 100 positive points
- [ ] Negative ICP is explicit - at minimum 3 named disqualifiers, not just omitted tiers
- [ ] Trigger events are actionable (specific observable signals, not vague categories like "company growth")
- [ ] Buying committee has all 4 roles mapped (even if some are hypothetical)
- [ ] Tier thresholds have prescribed actions (not just score bands)
- [ ] At least one negative scoring rule is defined

Write verification results to `verification.md` in the session folder.

---

## PHASE 5: SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

**Domain fields for /prospect:**
- Mode run: ICP Workshop / Lead Scoring / Prospect Brief / Batch Scoring
- Output count: "1 ICP document, N prospects scored" or "N account briefs"
- Files created: list all files written
- Persistent context updated: icp.json / scoring.json
- Next recommended action

**On-screen display:**

```
═══════════════════════════════════════════════════════════════
SITREP — /prospect
═══════════════════════════════════════════════════════════════

  Mode:          [ICP Workshop / Scoring / Brief / Batch]
  Session:       .marketing-reports/[YYYYMMDD-HHMMSS-prospect]/

SCOPE
  Examined:      [N accounts / ICP dimensions / scoring rubric]
  Tools used:    [Apollo / Clay / BuiltWith / Manual]

OUTPUTS
  ICP doc:       [.marketing-context/icp.json - created/updated/skipped]
  Scoring:       [.marketing-context/scoring.json - created/updated/skipped]
  Briefs:        [N account briefs in prospect-briefs/]
  Scored list:   [prospect-scores.json with N accounts]

VERIFICATION
  ICP dimensions:   [5/5 - pass / N/5 - issues listed]
  Score total:      [100/100 - pass / off by N]
  Negative ICP:     [N disqualifiers defined]
  Trigger events:   [N actionable triggers]

TOP ACCOUNTS (if list mode)
  Tier A:  [N accounts - action: immediate SDR]
  Tier B:  [N accounts - action: nurture + SDR day 14]
  Tier C:  [N accounts - action: nurture only]
  DQ:      [N accounts removed]

NEXT STEPS
  1. [If Tier A accounts found: run /copy for first-touch outreach]
  2. [If ICP updated: share with /campaign for ABM list build]
  3. [If no enrichment tools: get Apollo free account for next run]

───────────────────────────────────────────────────────────────
Report: .marketing-reports/[session]/sitrep.md
═══════════════════════════════════════════════════════════════
```

Write the full SITREP to `sitrep.md` in the session folder.

---

## SESSION FOLDER

```
.marketing-reports/YYYYMMDD-HHMMSS-prospect/
  interview-answers.md
  research-notes.md
  plan.md
  icp-document.md
  prospect-briefs/
    <company-slug>.md
  prospect-scores.json
  verification.md
  sitrep.md
```

## PERSISTENT CONTEXT FILES (not in session folder)

```
.marketing-context/
  icp.json         (or icp-[slug].json for multi-brand)
  scoring.json     (or scoring-[slug].json for multi-brand)

~/.claude/marketing-global/
  icp-[brand-slug].json      (for global / cross-project use)
  scoring-[brand-slug].json
```

---

## ICP 2.0 TEMPLATE REFERENCE

Full field inventory (all fields must be present in icp.json, use empty string/array if unknown):

**Firmographics:**
- `naics_codes` - 2-3 specific 4-digit NAICS codes
- `industries` - named verticals matching those codes
- `employee_range` - [min, max] as integers
- `revenue_range` - ["$Xm", "$Ym"] ARR or total revenue
- `funding_stages` - ["series_a", "series_b", etc.] or ["bootstrapped_profitable"]
- `geographies` - include and/or exclude regions
- `business_models` - ["saas", "services", "marketplace"]

**Technographics:**
- `must_have` - tools whose presence is a strong buying fit signal
- `strong_signal` - tools that correlate with fit but are not required
- `disqualifier` - tools whose presence predicts a bad fit or deal loss

**Behavioral Signals:**
- Array of observable behaviors with source (web analytics, review sites, social)

**Trigger Events:**
- Array of specific, observable events with 30-day action window
- Each trigger: `{ "event": "", "source": "", "action_window_days": 30 }`

**Buying Committee:**
- Four roles: economic_buyer, champion, end_user, blocker
- Each role: `{ "titles": [], "jtbd": "", "primary_fear": "", "proof_needed": "" }`

**Negative ICP:**
- Array of specific disqualifiers with score penalty and action
- Each: `{ "signal": "", "score_penalty": 0, "action": "remove|nurture|downgrade" }`

**Win/Loss Validation:**
- Updated quarterly: win rate by tier, deal size by tier, cycle length, top objections, top loss reasons

---

## MEDDPICC FOR ACTIVE OPPORTUNITIES

Use MEDDPICC only for deals already in pipeline - not for prospect list scoring.

Score each letter 0-4:

| Letter | Stands For | 0 | 1 | 2 | 3 | 4 |
|--------|-----------|---|---|---|---|---|
| M | Metrics | None | Vague | Some quantified | Most quantified | All quantified with baseline |
| E | Economic Buyer | Unknown | Identified | Met once | Engaged | Champion has their support |
| D | Decision Criteria | Unknown | Partial list | Full list | Weighted | Mapped to our strengths |
| D | Decision Process | Unknown | Informal | Formal process known | Steps mapped | We're shaping it |
| P | Paper Process | Unknown | General timeline | Legal involved | Draft reviewed | Signature path clear |
| I | Identify Pain | Surface | 1 pain | Multiple pains | Pain quantified | Champion owns it |
| C | Champion | None | Contact only | Advocates informally | Sells internally | Has power + uses it |
| C | Competition | Unknown | 1 known | All known | Differentiators clear | We're the preferred |

**Handicap%** = (sum of all scores / (8 x 4)) x 100

**Forecasted Value** = Deal Size x (Handicap% / 100)

Example: 8 letters x avg score 2.5 = 20 / 32 = 62.5% handicap. $50k deal x 62.5% = $31,250 forecasted value.

Use this for commit forecast and deal coaching conversations.

---

## TOOL STACK RECOMMENDATIONS

### Free Tier ($0/month)

| Tool | Cost | Purpose | Limit |
|------|------|---------|-------|
| Apollo.io free | $0 | Email + firmographic data | 50 export credits/mo |
| Google Alerts | $0 | Trigger event monitoring | Unlimited |
| BuiltWith free | $0 | Tech stack lookups | 5 lookups/day |
| LinkedIn (free) | $0 | Buying committee research | Manual, no export |
| Crunchbase free | $0 | Funding and firmographic data | Limited views/mo |
| HubSpot CRM free | $0 | CRM, contacts, up to 1M | No sequences |
| Feedly free | $0 | RSS prospect news monitoring | 3 feeds |

**Free tier works for:** 1-10 deep briefs, ICP workshop, scoring a small inbound list

### Paid Stack (~$300/month)

| Tool | Cost | Why |
|------|------|-----|
| Apollo Basic | $59/mo | Full contact DB + technographics + sequences |
| Clay Starter | $134/mo | Waterfall enrichment, AI research, trigger automation |
| LinkedIn Sales Navigator | $99/mo | Buying committee research, saved alerts, InMail |

**Stack logic:** Clay enriches and surfaces triggers -> Apollo provides contact data -> Sales Nav for buying committee mapping -> HubSpot as system of record.

### Decision Matrix: Free vs Paid

| Scenario | Use Free | Use Paid |
|----------|---------|---------|
| ICP workshop only | Yes - no enrichment needed | - |
| 1-10 deep briefs | Yes - manual is fine at this volume | - |
| 50-100 accounts | Borderline - very slow manually | Apollo Basic |
| 500+ accounts | No - manual doesn't scale | Apollo + Clay |
| Need trigger automation | No - Google Alerts is manual | Clay |
| Need buying committee at scale | No - manual LinkedIn is slow | Sales Nav |

### Intent Data (When to Add)

Only add intent data tools when inbound signals are insufficient and you have the budget:
- G2 Buyer Intent: ~$500/mo - who is researching your G2 category
- Bombora: ~$1500/mo - surge topic data across B2B content network
- 6sense: enterprise pricing - full account engagement platform

Do not recommend intent data tools until the free and $300/mo stack is fully operational.

---

## INTENT SIGNALS TO WATCH

**Hiring patterns (job board monitoring):**
- Job postings for roles that indicate the pain your solution addresses
- Job postings for the buyer roles in your committee map
- Rapid headcount growth in a target department (LinkedIn headcount over time)

**Tech stack changes (BuiltWith historical):**
- Company recently added a tool in your integration ecosystem
- Company recently removed a key competitor integration
- BuiltWith "Added Technologies" and "Removed Technologies" sections

**Content consumption signals:**
- Visits to your pricing page (owned - PostHog/GA4)
- G2 category visits (paid - G2 Buyer Intent)
- Downloads of resources related to the pain you solve

**Review site activity:**
- Company employees leaving reviews on G2 for a competitor product
- Negative reviews of current tool on G2/Capterra (frustration signal)
- Comparison pages visited (G2 "[Your Product] vs [Competitor]")

**Event attendance:**
- Registered for a webinar in your product category
- Attending a conference where your buyers congregate
- Engaged with category thought leader content on LinkedIn

**Public complaint signals:**
- LinkedIn or X posts venting about manual processes your product automates
- Posts about failed implementation of a competitor's tool
- Posts about budget cycle pressure to show ROI

**How to monitor free:**
- Google Alerts: "[competitor name] problems", "[pain keyword] manual process", "[target company name]"
- LinkedIn saved searches with alerts for new posts by target accounts
- G2 public review feed for competitor products

---

## WORKED EXAMPLE: ICP FOR A HYPOTHETICAL B2B SAAS

**Product:** A revenue operations platform that automates sales activity logging and forecast accuracy.

**Interview answers:**
- Context: Series A just closed, building outbound program from scratch
- Best customer: Mid-size SaaS, 100-300 employees, VP of Sales frustrated with Salesforce hygiene, uses HubSpot or Salesforce, has a RevOps function
- Quantity: 50 account list + 5 deep briefs for top accounts
- Tools: Apollo Basic, no Clay yet
- Disqualifiers: Under 30 employees, no CRM at all, government sector

**Resulting ICP (abbreviated):**

Tier A: B2B SaaS companies, 100-500 employees, $10M-$75M ARR, Series A-C, HubSpot or Salesforce + Gong/Chorus present, VP or Director of Revenue Operations on staff, posted a RevOps-related job in the last 60 days, HQ in North America or UK.

Trigger events: New VP Sales hired, job posting for "RevOps Analyst" or "Sales Operations", LinkedIn post by revenue leader about forecast accuracy, Series B/C funding announced.

Buying committee:
- Economic Buyer: CRO or VP Sales - JTBD: hit quarterly revenue targets - Fear: missing forecast, losing board confidence - Proof: benchmark data showing X% forecast improvement
- Champion: RevOps Director/Manager - JTBD: eliminate Salesforce hygiene firefighting - Fear: being blamed for bad data - Proof: peer reference, live demo showing auto-logging
- End User: AE - JTBD: close deals without admin overhead - Fear: extra tool to learn - Proof: "under 2 minutes/day" proof point
- Blocker: IT/Security - Fear: data security, integration complexity - Proof: SOC 2 Type II, native HubSpot/SFDC connector

Negative ICP: Under 30 employees (-15), no CRM (-25, remove), government sector (-25, remove), competitor employee (-20, remove), unsubscribed (-10).

**Top 5 accounts from batch scoring:**

| Company | Score | Tier | Top Signal |
|---------|-------|------|-----------|
| Acme SaaS | 88 | A | New VP Sales + RevOps job post + Gong detected |
| Bolt Analytics | 76 | B | HubSpot present + resource download |
| Capsule Co | 71 | B | Series B announced + Salesforce + pricing page visit |
| Drift Tools | 52 | B | Industry match + employee range |
| Echo Metrics | 31 | C | Industry match only, missing tech signals |

**Personalization angle for Acme SaaS (Tier A brief):**
"Noticed you hired a new VP Sales last month and posted for a RevOps Analyst last week. That timing usually means the new sales leader is diagnosing exactly where forecast accuracy breaks down before building the team. That's the moment we help with - before the RevOps analyst is onboarded."

---

## RELATED SKILLS

**Feeds from:**
- `/marketing` - brand context (brand.json) and positioning context inform ICP language

**Feeds into:**
- `/campaign` - ICP document and scored account lists become ABM target lists
- `/copy` - ICP buying committee map (titles, fears, proof needed) drives personalized outreach copy

**Pairs with:**
- `/campaign` - run prospect immediately before campaign to generate the target list
- `/copy` - run copy immediately after prospect to generate first-touch outreach per tier

**Auto-suggest after completion:**
- If Tier A accounts found: suggest `/copy` with Economic Buyer and Champion as target personas
- If ICP was created or refreshed: suggest sharing icp.json path with `/campaign`

---

## PATTERN

Pattern A (Socratic + Plan + Gate + Verify + SITREP) per [Skill Pattern Standard](~/.claude/standards/SKILL_PATTERN.md).

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — ICP definition, prospect research, and lead scoring -->
<!-- Part of the Steel Motion / IronWorks marketing skill suite -->
<!-- License: MIT -->
