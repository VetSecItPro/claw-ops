---
description: "/marketing — Full marketing campaign pipeline for any product: strategy, messaging, channel plan, assets, launch calendar. Works for SaaS, agencies, ecommerce, services."
allowed-tools: Bash(git *), Bash(mkdir *), Bash(ls *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, Skill, WebFetch, WebSearch
---

# /marketing — Marketing Campaign Pipeline

**Steel Principle: No campaign ships without a defined target audience, a single core message, and at least one measurable goal. Strategy first, assets second.**

A full-stack marketing planning skill for any product, industry, or audience. Give it a product and a goal; it returns a strategy, messaging framework, channel plan, asset checklist, and launch calendar - all ready to execute.

**FIRE AND FORGET** — Describe what you're marketing and what you want to achieve. The skill handles the rest.

> **When to use /marketing:**
> - Launching a new product or feature
> - Planning a seasonal or campaign push
> - Building a go-to-market strategy from scratch
> - Auditing and improving existing marketing for a product
> - Creating a messaging framework for a new audience segment
>
> **When NOT to use /marketing:**
> - You just need a single piece of copy - use `/copy` instead
> - You just need social posts for today - use `/social` instead
> - You need technical SEO or site performance work - use `/perf` or `/sec-ship`

---

## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** No completion claim without a documented strategy that passes the ICP test (Ideal Customer Profile defined, core pain identified, core message validated against that pain)
- **Steel Principle #2:** Assets are never created before strategy is approved - no copy, no creative briefs, no calendars until the messaging framework is signed off
- **Steel Principle #3:** Every channel recommendation must be justified by audience evidence, not convention ("everyone uses Instagram" is not evidence)

### Marketing-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The target audience is obvious, skip ICP definition" | Vague audiences produce vague messaging that converts nobody | Define ICP: job title, pain, trigger, objection - before writing a word |
| "We can define the goal later, let's get the copy done first" | Copy without a goal optimizes for nothing; you can't measure what you didn't define | State the measurable goal (signups, demos, revenue) before any assets |
| "This channel worked for a similar company, use it" | Your audience may not be where that company's audience was | Validate channel against where YOUR ICP actually spends time |
| "The message is self-evident from the product features" | Features are not benefits; benefits are not messages; messages must map to a specific pain | Run the message through the "so what?" test from the ICP's perspective |
| "We'll A/B test later, just pick one direction" | "Later" never comes; bake at least two message variants in from the start | Define primary and secondary message angles in the framework |
| "The budget is flexible, we can scope it later" | Open-ended budgets produce scope creep; every channel plan needs a budget assumption | Establish budget tier (bootstrap / growth / scale) before channel plan |

---

## STATUS UPDATES

> Reference: [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)

```
📣 /marketing Started
   Product: [product name]
   Goal:    [stated goal]
   Stage:   [launch / growth / retention / reactivation]

📊 Phase 1: Discovery & ICP
   ├─ Product context gathered
   ├─ ICP defined
   └─ Core pain validated

🎯 Phase 2: Strategy
   ├─ Positioning statement drafted
   ├─ Messaging framework built
   └─ ✅ Strategy approved

📡 Phase 3: Channel Plan
   ├─ Channels evaluated: [count]
   ├─ Primary channels selected: [list]
   └─ Budget tier confirmed

🗓️  Phase 4: Launch Calendar
   ├─ Timeline mapped
   ├─ Asset checklist generated
   └─ ✅ Calendar ready

📋 Phase 5: Asset Production
   ├─ Copy assets drafted: [count]
   ├─ Creative briefs written: [count]
   └─ ✅ Assets ready for review

📧 Phase 6: Newsletter Strategy (if applicable)
   ├─ Issue structure defined
   ├─ Cadence confirmed
   └─ ✅ Metrics baseline set

🗂️  Phase 7: Content Pillars (if applicable)
   ├─ Pillar exercise completed
   ├─ Pillars documented
   └─ ✅ Sticky rule applied

🔬 Phase 8: Primary Research Plan (if applicable)
   ├─ Research formats selected
   ├─ Questions drafted
   └─ ✅ Publishing plan confirmed

✅ Campaign Ready
   Deliverables saved to: .marketing-reports/[slug]/
```

---

## CONTEXT MANAGEMENT

> Reference: [Context Management Protocol](~/.claude/standards/CONTEXT_MANAGEMENT.md)

For large campaigns spanning many assets, sub-agents handle individual deliverables and write outputs to `.marketing-reports/[campaign-slug]/`. The orchestrator (this skill) maintains the strategy doc as the single source of truth. Sub-agents must read the strategy doc before producing any asset.

---

## AGENT ORCHESTRATION

> Reference: [Agent Orchestration Protocol](~/.claude/standards/AGENT_ORCHESTRATION.md)

When producing multiple assets (email sequence, ad copy variants, landing page copy, social calendar), dispatch sub-agents per asset type. Each sub-agent receives:
1. The approved strategy doc
2. The messaging framework
3. The specific asset brief

Never dispatch asset sub-agents until the strategy is approved in Phase 2.

---

## OUTPUT STRUCTURE

```
.marketing-reports/
├── [campaign-slug]/
│   ├── CAMPAIGN.md             # Master campaign doc (strategy + calendar)
│   ├── icp.md                  # Ideal Customer Profile
│   ├── messaging-framework.md  # Positioning, messages, proof points
│   ├── channel-plan.md         # Channels, rationale, budget allocation
│   ├── launch-calendar.md      # Timeline with asset dependencies
│   ├── asset-checklist.md      # Every asset needed, owner, status
│   └── assets/
│       ├── email/
│       ├── ads/
│       ├── landing-page/
│       └── social/
```

---

## PHASE 0: INTAKE

Parse the user's request for:

- **Product/Service:** What is being marketed? What does it do? Who is it for?
- **Goal:** What does success look like? (leads, signups, revenue, awareness, event attendance)
- **Timeline:** When does this need to launch or be complete?
- **Stage:** Launch (brand new), Growth (existing, scaling), Retention (keeping customers), Reactivation (churned customers)
- **Budget Tier:** Bootstrap (DIY, minimal spend), Growth ($1k-$50k/mo), Scale ($50k+/mo)
- **Existing assets:** What already exists? (website, brand guide, prior campaigns)

If any of these are missing, ask for them directly. Do not proceed to Phase 1 without product, goal, and timeline.

**Setup:**
```bash
mkdir -p .marketing-reports
grep -q ".marketing-reports" .gitignore 2>/dev/null || echo ".marketing-reports/" >> .gitignore
```

Display intake summary and confirm with user before proceeding.

---

## PHASE 1: DISCOVERY & ICP

### 1.1 Research the Product Context

If the user provides a URL, use WebFetch to extract:
- What the product does (core function)
- Who it claims to serve
- Current positioning language
- Existing social proof (testimonials, case studies, metrics)
- Pricing model

If no URL, gather from conversation.

### 1.2 Define the Ideal Customer Profile (ICP)

Build the ICP document with these fields:

```markdown
## Ideal Customer Profile

**Primary ICP:**
- Role/Title: [who makes the buying decision]
- Company type: [size, industry, stage - if B2B]
- Demographics: [if B2C: age, location, situation]
- Primary Pain: [the specific problem they're struggling with RIGHT NOW]
- Trigger Event: [what makes them search for a solution today vs. 6 months ago]
- Current Solution: [what they're doing instead - spreadsheet, competitor, nothing]
- Key Objection: [the #1 reason they won't buy]
- Success Metric: [how they define "this worked"]

**Secondary ICP (if applicable):**
[same structure]
```

If the user hasn't defined their ICP, draft a hypothesis based on the product, ask them to validate, then finalize.

### 1.3 Validate Core Pain

Run the "hair on fire" test: Is the pain bad enough that the ICP is actively looking for a solution, or is this a vitamin (nice to have)?

- **Painkiller:** Active problem, budget unlocked, urgency exists -> proceed
- **Vitamin:** Nice to have, low urgency -> flag this to the user; vitamin marketing requires much more volume and education

Document the finding in the ICP doc.

---

## PHASE 2: STRATEGY & MESSAGING FRAMEWORK

### 2.1 Positioning Statement

Draft a positioning statement using this template:

```
For [ICP role/situation],
[Product name] is the [category]
that [core benefit / unique value]
unlike [primary alternative],
[product name] [key differentiator].
```

Example using a hypothetical B2B project management SaaS:
```
For freelancers managing 5+ clients simultaneously,
Acme Projects is the client portal tool
that eliminates status-update emails by giving clients real-time visibility,
unlike spreadsheet chaos or generic PM tools,
Acme Projects is purpose-built for the freelancer-client relationship.
```

Present the positioning statement. Ask for feedback. Revise until approved.

### 2.2 Messaging Framework

Build a 3-tier message hierarchy:

**Tier 1 - Primary Message (the headline):**
One sentence. Addresses the core pain directly. No jargon.

**Tier 2 - Supporting Messages (the "why us"):**
3-4 benefit statements, each tied to a proof point:
- [Benefit]: [Proof point - stat, testimonial, feature that delivers it]

**Tier 3 - Proof & Credibility:**
- Social proof: [customers, case studies, review scores]
- Risk reducers: [trial, guarantee, no-credit-card, etc.]
- Authority: [press mentions, certifications, notable customers]

**Message Variants:**
Define a primary angle and one alternative angle:
- **Primary:** [pain-focused - leads with the problem]
- **Alt:** [outcome-focused - leads with the result]

### 2.3 Strategy Sign-Off

Present the full strategy (ICP + positioning + messaging framework) in one clean document. 

**HARD GATE:** Do not proceed to Phase 3 until the user explicitly approves the strategy.

```
═══════════════════════════════════════════════════════════════
STRATEGY REVIEW
═══════════════════════════════════════════════════════════════

  ICP:          [primary ICP one-liner]
  Positioning:  [positioning statement]
  Core Message: [Tier 1 message]
  Goal:         [stated goal + metric]
  Timeline:     [launch date or window]

  Does this accurately reflect your product and audience?
  Approve to proceed to channel planning.
═══════════════════════════════════════════════════════════════
```

---

## PHASE 3: CHANNEL PLAN

### 3.1 Channel Evaluation Matrix

Evaluate channels against three criteria:
1. **Audience fit:** Is the ICP actually on/in this channel?
2. **Budget fit:** Does this channel work at the stated budget tier?
3. **Timeline fit:** Can this channel deliver results within the timeline?

Score each channel 1-3 on each criterion. Include only channels scoring 7+.

**Common channels to evaluate:**

| Channel | Best for | Budget tier | Time to results |
|---------|----------|-------------|-----------------|
| Organic search (SEO) | B2B SaaS, high-intent buyers | Bootstrap+ | 3-12 months |
| Paid search (Google/Bing Ads) | High-intent, active searchers | Growth+ | Days to weeks |
| LinkedIn organic | B2B, thought leadership | Bootstrap+ | 1-3 months |
| LinkedIn paid | B2B, precise job-title targeting | Growth+ | Days to weeks |
| Email (owned list) | Nurture, retention, reactivation | Bootstrap+ | Immediate |
| Content marketing (blog/video) | SEO + trust building | Bootstrap+ | 2-6 months |
| Paid social (Meta, TikTok) | B2C, visual products, awareness | Growth+ | Days to weeks |
| Referral/affiliate | Any - if product has happy users | Bootstrap+ | 1-3 months |
| Community/forums | Niche audiences, trust-first | Bootstrap+ | 1-6 months |
| Partnerships/co-marketing | Complementary audience access | Bootstrap+ | 1-3 months |
| Cold outbound email | B2B, specific ICP targeting | Bootstrap+ | Weeks |
| PR/earned media | Launches, credibility building | Bootstrap+ | Weeks to months |

### 3.2 Primary Channel Selection

Select 2-3 primary channels and 1-2 supporting channels.

**Primary channels:** Where the majority of effort and budget goes. Chosen for best fit across all three criteria.

**Supporting channels:** Amplify primary channel results. Lower effort, lower spend.

Document rationale for each selection. "It's popular" is not a rationale. Use: "ICP [role] actively searches [query] on [channel], and our budget tier supports [spend approach]."

### 3.3 Budget Allocation

Allocate budget across channels as percentages, not fixed amounts (unless the user provided a specific budget):

```
Bootstrap tier example:
  70% → [highest-fit organic channel]
  20% → [supporting channel]
  10% → experimentation budget

Growth tier example:
  50% → [primary paid channel]
  30% → [secondary paid or organic]
  20% → [retention or referral]
```

---

## PHASE 4: LAUNCH CALENDAR

### 4.1 Build the Timeline

Work backward from the launch/goal date. Define phases:

- **Pre-launch (T-minus 4-8 weeks):** Audience building, teaser content, SEO foundation, list building
- **Launch week:** Full channel activation, PR push, email to list, paid spend ramp
- **Post-launch (weeks 2-8):** Nurture sequences, retargeting, content cadence, optimization based on early data

For each phase, list:
- Key activities
- Owner (user, contractor, AI, or TBD)
- Assets required (links to asset checklist)
- Success metric for this phase

### 4.2 Asset Checklist

Generate a complete list of every asset needed to execute the campaign:

```markdown
## Asset Checklist

### Website / Landing Page
- [ ] Hero headline + subhead
- [ ] Features/benefits section copy
- [ ] Social proof section
- [ ] CTA copy
- [ ] Meta title + description (SEO)

### Email
- [ ] Welcome / lead magnet delivery email
- [ ] Nurture sequence (specify # of emails)
- [ ] Launch announcement email
- [ ] Follow-up / re-engagement email

### Paid Ads (if applicable)
- [ ] Search ad headlines (15 variants)
- [ ] Search ad descriptions (4 variants)
- [ ] Social ad copy (3 variants per format)
- [ ] Ad creative brief (dimensions, visual direction)

### Content / Organic
- [ ] Blog post outlines (specify topics)
- [ ] LinkedIn posts (specify cadence)
- [ ] Case study template

### Sales Enablement (if B2B)
- [ ] One-pager / leave-behind
- [ ] Objection handling guide
- [ ] Email templates for outbound

### Misc
- [ ] [Any product-specific assets]
```

Present the full asset checklist and confirm with user which assets they want produced in Phase 5.

---

## PHASE 5: ASSET PRODUCTION

Produce only the assets the user confirmed in Phase 4. For each asset:

1. Reference the approved messaging framework (Tier 1, 2, 3 messages)
2. Apply the ICP's voice and pain points
3. Follow any brand voice guidelines provided
4. Produce 2 variants minimum for testable assets (ads, emails, CTAs)

**For large asset sets:** Dispatch sub-agents per asset type. Each sub-agent receives the messaging framework as system context.

**Asset production order:**
1. Landing page / hero copy (highest leverage, everything else links to it)
2. Email sequences (owned channel, highest ROI)
3. Ad copy (if paid channels selected)
4. Content outlines (if organic/content channels selected)
5. Remaining checklist items

For each completed asset, mark it in the asset checklist.

---

## PHASE 6: NEWSLETTER STRATEGY (when email is a primary or supporting channel)

### 6.1 Issue Structure

Every newsletter issue follows this structure - no exceptions:

- **Subject line:** Specific beats clever. "What we learned from 6 months of LinkedIn experiments" outperforms "The content playbook you've been waiting for." If the subject could apply to any issue from any newsletter, rewrite it.
- **Opening line:** One sentence. A specific situation, a number, or a scene. Never "Hey [first name]!" or "Happy Tuesday!" The opening line is the real subject line - it determines whether the email gets read.
- **Body:** 200-600 words. One idea, fully developed. If you have two ideas, that is two issues.
- **So what:** 2-3 sentences connecting the idea to the reader's situation without over-explaining.
- **CTA (if commercial):** Soft, relevant, placed only after value has been delivered. One CTA per issue.
- **Signature:** Short. Name plus one-line context. No "P.S. forward this to a friend" unless the issue genuinely earned it.

**Exclude from every issue:** Multiple CTAs, housekeeping sections, "in case you missed it" recaps, share-for-sake asks, and unearned calls to forward.

### 6.2 Cadence Rules

- **Default cadence:** Weekly. This is the floor that maintains momentum without fatiguing the list.
- **Biweekly:** Acceptable only if quality consistently requires the additional time. Slower cadences lose compounding list trust.
- **Daily:** Only works for curation-only formats (e.g., Ben's Bites model). Opinion and analysis at daily cadence burns lists.

### 6.3 Voice Rule

Write to one person. Name them in your head before writing the first sentence - a real subscriber, not a persona. The first draft is a message to that person. If you wouldn't say it to a smart colleague, cut it.

### 6.4 Metrics That Matter (2026)

**Dead metrics - do not optimize for these:**
- Raw open rate (inflated by Apple Mail Privacy Protection)
- Follower/subscriber count in isolation
- Impressions

**Real metrics:**
- Reply rate (0.5-1% on a 5,000-person list = 25-50 real responses; this is the signal)
- CTOR (click-to-open rate) - filters out MPP noise
- Forward/share rate - indicates content earned its distribution
- Subscriber-to-opportunity conversion - the only metric that closes the loop to revenue

**Honest test:** Did a qualified person reach out this week because of something published? Did someone mention they shared it? If yes to either, the newsletter is working.

---

## PHASE 7: CONTENT PILLARS

### 7.1 Pillar Definition Exercise

Run this three-part exercise before producing any content calendar. Document results in the campaign strategy file.

**Step 1:** List the 5 conversations you (or the brand) have had most often with customers or peers in the last 6 months. Not topics you think are important - actual conversations that keep recurring.

**Step 2:** List 3 things you have data on that most people in this space operate on assumption about. This is where primary research lives.

**Step 3:** List 2 mistakes that cost you the most and that you see others about to repeat.

The intersection of these three inputs is the brand's content territory. Everything produced should trace back to one of these pillars.

**Examples of well-defined pillars:**
- Lenny Rachitsky: PM craft / PM career development / company case studies / hiring and team dynamics
- Morgan Housel: Behavioral psychology on money and business / history as lens / gap between knowing and doing / why smart people are wrong about the future

### 7.2 Sticky Rule

Before producing any post, email, or content asset, connect it to a pillar in one sentence. If you cannot make that connection in one sentence, the content is off-pillar. Flag it and either reframe it or hold it.

This rule prevents the most common content calendar failure: producing volume without coherence, which produces impressions without trust.

---

## PHASE 8: PRIMARY RESEARCH AS CONTENT

When the channel plan includes content marketing or LinkedIn organic, primary research is the highest-leverage content investment. It produces a long-term moat that AI-generated content cannot replicate.

### 8.1 Research Formats

**LinkedIn poll methodology:**
- 100+ responses = citable in content
- Question framing: binary or 3-4 option choices, not open-ended
- Post immediately after poll closes with your analysis of the results
- Structure: "I polled [N] [ICP role] about [X]. Here's what surprised me."

**Typeform/survey to existing customers:**
- 5-7 questions maximum; longer surveys have significant drop-off
- Focus on behaviors and past decisions, not opinions about hypotheticals
- One open-ended question at the end: "What's the one thing you wish you'd known before [decision]?"
- Results feed at minimum 3 pieces of content: a summary post, a deep-dive post, and a newsletter issue

**CRM data analysis:**
- Look for patterns in deal velocity, objection frequency, feature adoption rate, churn timing
- "Our data shows X% of [ICP] who [behavior] within [timeframe] reach [outcome]" is more credible than any borrowed statistic
- Gong Labs and Ramp editorial are the reference models: specific, sourced, and counterintuitive

**"I asked 20 [role] about X" post structure:**
1. State the question and sample (specific number and role)
2. Name the most surprising finding
3. Give 2-3 supporting data points
4. Name the exception or minority view
5. State what you changed because of the data (behavioral output, not just belief)

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Marketing-Specific Cleanup

1. All reports and assets are in `.marketing-reports/[campaign-slug]/` - already gitignored
2. No servers or browsers opened - nothing to close
3. If WebFetch was used for competitor research, no credentials or sessions to clear
4. Strategy doc is the single source of truth - keep it, don't split it across files

---

## SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```
═══════════════════════════════════════════════════════════════
SITREP — MARKETING CAMPAIGN
═══════════════════════════════════════════════════════════════

  Product:       [product name]
  Campaign:      [campaign name / goal]
  Stage:         [launch / growth / retention]
  ICP:           [primary ICP one-liner]
  Core Message:  [Tier 1 message]

  Channels:      [primary channels, comma-separated]
  Budget Tier:   [bootstrap / growth / scale]
  Timeline:      [start → launch date]

  Assets Produced:
    ✅ [asset 1]
    ✅ [asset 2]
    ⏳ [asset 3 - pending user input]

  Reports:       .marketing-reports/[campaign-slug]/
  Status:        STRATEGY_APPROVED / ASSETS_IN_PROGRESS / CAMPAIGN_READY

═══════════════════════════════════════════════════════════════
                    Next action: [clear next step]
═══════════════════════════════════════════════════════════════
```

---

## RELATED SKILLS

**Feeds from:**
- `/brainstorm` - approved spec for a new product/feature is the ideal starting point for a marketing strategy
- `/mdmp` - the selected COA for a major launch or positioning change provides the strategic frame
- (otherwise /marketing is the strategy source; it creates the framework others consume)

**Feeds into:**
- `/copy` - the messaging framework (ICP, positioning, Tier 1/2/3 messages) is the required input for any copy asset
- `/social` - channel plan and brand voice from the campaign brief drive social content strategy
- `/blog` - content calendar and topic ideas from the channel plan feed article topics
- `/subagent-dev` - for multi-asset production, dispatch the asset checklist as parallel tasks with two-stage review

**Pairs with:**
- `/copy` - run immediately after strategy approval to produce the high-priority assets from the checklist
- `/social` - run together when the campaign needs both strategic assets and immediate social cadence
- `/blog` - run when content marketing is a primary channel; blog articles execute the content calendar

**Auto-suggest after completion:**
When strategy is approved and asset checklist is confirmed, suggest: `/copy` for landing page and email assets, then `/social` for social content calendar

---

## GLOBAL BRAND CONTEXT

Marketing context can be stored at two levels:

**Project-local (default):** `.marketing-reports/brand.json` in the current project directory. Takes precedence when present.

**Global (reusable across products):** `~/.claude/marketing-global/brand-[name].json` - for multi-product agencies and umbrella brands.

When `/marketing` starts:
1. Check for `.marketing-reports/brand.json` in the project directory - if found, use it
2. If not found, check `~/.claude/marketing-global/` for named brand files
3. If global brands exist, offer to pick: "Found [N] global brands: [list]. Use one, or create new context?"
4. When creating new context, ask: "Save as global (reusable across projects) or project-local (this project only)?"
   - Global: save to `~/.claude/marketing-global/brand-[name].json`
   - Local: save to `.marketing-reports/brand.json`

**Global brand file format:**
```json
{
  "brand_name": "Steel Motion LLC",
  "tagline": "Build faster. Ship smarter.",
  "icp_primary": "Veteran-owned SaaS founders, 1-10 person teams",
  "core_pain": "Scaling solo without burning out",
  "voice": "Direct, technical, no fluff",
  "tone_modifiers": { "linkedin": "professional authority", "x": "builder-to-builder" },
  "products": ["rowan", "kaulby", "clarus", "styrby"],
  "proof_points": ["150+ deployments shipped", "4 SaaS products in parallel"],
  "avoid": ["buzzwords", "em-dashes", "sparkle icons"]
}
```

---

## REMEMBER

- **Strategy before assets** - the messaging framework is the foundation; copy built on a weak foundation will underperform no matter how clever it is
- **ICP specificity is everything** - "small businesses" is not an ICP; "solo accountants billing 10-20 clients monthly who are drowning in back-and-forth emails" is an ICP
- **Channel rationale, not channel convention** - justify every channel with audience evidence
- **Two variants minimum on testable assets** - optimization requires something to compare
- **The asset checklist is a contract** - don't produce undocumented assets; don't skip documented ones

---

## MODULE A: MESSAGING MATRIX

**Self-contained - run this module independently when you need to align messaging across personas and funnel stages.**

A messaging matrix is a strategic grid that ensures every persona hears the right message at the right stage. It is the single source of truth that prevents sales, marketing, and content from speaking different languages to the same buyer.

Source: Cascade Insights, CoSchedule, and the R.P.S. (Role-Pain-Stage) framework.

### When to Build a Messaging Matrix

- You have more than one persona (e.g., Champion + Economic Buyer + End User)
- Your funnel has meaningful stages where the conversation shifts (awareness vs. late-stage evaluation)
- Your sales and marketing teams are writing copy independently and producing inconsistent messages
- You are running ABM and need account-specific messaging per buying committee role

### The Template

For each persona, fill every cell with a SPECIFIC phrase they would hear - not a category label.

```markdown
## Messaging Matrix: [Product Name]

| Persona | Awareness Stage | Consideration Stage | Decision Stage |
|---------|----------------|--------------------|--------------------|
| [Role]  | What they feel, the problem they're living with | What they compare, top objection vs. alternatives | What seals it, risk reducer + proof |

### How to Fill Each Cell

**Awareness cell:** The pain in their words, not your words.
- Wrong: "Lack of operational visibility"
- Right: "I have no idea what my team actually worked on this week"

**Consideration cell:** What they compare you against + the one differentiator that wins.
- Wrong: "Superior feature set"
- Right: "Unlike [Competitor], we don't require a 6-month implementation before you see results"

**Decision cell:** The specific proof point + the risk reducer that closes hesitation.
- Wrong: "Strong ROI"
- Right: "[Customer] reduced invoice processing time by 60% in 30 days - and there's no credit card required to start"

### Worked Example (B2B Project Management SaaS)

| Persona | Awareness | Consideration | Decision |
|---------|-----------|---------------|----------|
| Agency owner (Economic Buyer) | "I'm spending 6 hours/week on client status calls that shouldn't need to happen" | "Other tools require the whole team to change how they work - this only requires me to change one process" | "[Agency] cut client calls by 40% in 90 days - try it free for 14 days, cancel anytime" |
| Project manager (Champion) | "Clients keep asking me for updates I already sent them" | "Unlike Asana, this is built specifically for the client-facing workflow, not internal tracking" | "Setup takes 20 minutes - I can have my first client project running before my afternoon call" |
| Developer/end user | "I hate updating status in three different places" | "The API means I can push status from our existing tools - no manual re-entry" | "One integration, five minutes - our lead dev set it up in a single sprint" |

### Proof Point Pairing

For every decision-stage cell, pair a message with:
1. A named customer result (or placeholder marked clearly)
2. A risk reducer: free trial, money-back guarantee, no credit card, or a named reference call

### CTA by Stage

| Stage | CTA Type | Example |
|-------|----------|---------|
| Awareness | Educate, no ask | "See how agencies handle this" (links to case study or guide) |
| Consideration | Low-friction next step | "Book a 15-minute comparison call" or "See a 5-minute demo" |
| Decision | Direct, specific | "Start your 14-day free trial - no credit card" |
```

### Output

Save the completed matrix to `.marketing-reports/[slug]/messaging-matrix.md`. This file is the required input for any `/copy` asset production run.

---

## MODULE B: BRAND VOICE AND TONE GUIDE

**Self-contained - run this module independently when you need to document and operationalize a brand's voice.**

A voice guide that sits in a folder and never gets used is not a voice guide - it is a planning artifact. This module produces a guide specifically designed to be READ and USED by anyone writing for the brand, not filed as a reference document.

Source: Mailchimp Content Style Guide, Slack API Design Voice principles, Lumen & Fox voice guide methodology.

### What Makes a Voice Guide Work vs. Decoration

Decoration: "We are friendly, professional, and innovative." (Every brand says this. It helps no one write anything.)

Working guide: Specific DO / DON'T pairs, named patron saints, and situation-specific tone shifts so a writer knows exactly how to handle "we made a mistake" differently from "we shipped a feature."

### Step 1: Define the Voice in 3 Adjectives

Do NOT use: friendly, professional, innovative, passionate, customer-centric.

Use adjectives that would exclude other brands. Test: if the adjective applies to both your brand and a hospital and a luxury hotel, it is not specific enough.

Examples of specific adjectives:
- Mailchimp: "Plainspoken, genuine, translators" (not "friendly")
- Slack: "Wit without buffoonery" (not "fun")
- Basecamp: "Opinionated, direct, uncomfortable with corporate-speak"

Produce three adjectives. For each, write one sentence explaining what it means in practice.

```markdown
## Our Voice

**[Adjective 1]:** [What this means for how we write - one concrete sentence]
**[Adjective 2]:** [What this means for how we write - one concrete sentence]
**[Adjective 3]:** [What this means for how we write - one concrete sentence]
```

### Step 2: DO / DON'T Table (Minimum 10 Rows)

Produce at least 10 rows. Each row is a specific writing choice, not a general principle.

```markdown
## DO / DON'T

| DO Say | DON'T Say | Why |
|--------|-----------|-----|
| "You'll save 3 hours a week" | "Significant time savings" | Specific beats vague; the number does the work |
| "Here's what went wrong and what we're doing about it" | "We apologize for any inconvenience" | Own it specifically; "inconvenience" minimizes real impact |
| "This works for teams of 5-50" | "Scales with your business" | Name the actual size range; "scales" means nothing |
| "No credit card required" | "Start your free journey today" | Concrete friction-reducer vs. marketing puffery |
| "We got this wrong" | "Mistakes were made" | Active voice, first person, no passive deflection |
| "Setup takes 10 minutes" | "Seamless onboarding experience" | Specific time beats adjective claims |
| "Ask us anything - reply to this email" | "Don't hesitate to reach out" | "Don't hesitate" is filler; direct invitation is an action |
| "Your team sees exactly what's in progress, what's blocked, and what shipped" | "Full project transparency" | Show the specific benefit, not the feature category |
| "We've shipped X features this quarter based on what you asked for" | "We're committed to continuous improvement" | Named actions beat stated commitments |
| "If this isn't working for you, cancel anytime - no questions" | "We value your business" | Risk reducer stated plainly; "value your business" is a cliche |
```

### Step 3: Voice by Situation

How the voice SHIFTS (not changes) across situations. The personality stays the same; the tone adjusts.

```markdown
## Voice by Situation

**Celebrating a win (product launch, customer milestone):**
- Keep pride specific, not effusive. "We shipped X after 6 months of work" > "We're SO excited to announce"
- Name who made it happen (team, customer co-designers)
- One exclamation point maximum per piece

**Delivering bad news (outage, price change, mistake):**
- Lead with the fact, not the apology. "Our service was down for 47 minutes on [date]. Here's what happened."
- Explain the root cause without jargon
- State the specific fix and the timeline - never "we're working on it" without a date
- Avoid: "We sincerely apologize for any inconvenience this may have caused"

**Marketing copy (ads, landing pages, emails):**
- Pain-led or outcome-led - pick one per asset, don't mix
- No superlatives without proof: "the most" requires evidence
- CTAs in first person: "Start my free trial" outperforms "Start your free trial" (90% CTR lift in ContentVerve research)

**Support responses:**
- Start with acknowledgment of the specific problem, not a greeting
- One sentence on what you are doing RIGHT NOW
- One sentence on timeline
- One sentence on how to reach a human if the automated response didn't help
```

### Step 4: Patron Saints

Name 2-3 brands or writers this voice aspires to. Not to copy - to orient.

```markdown
## Patron Saints

These are NOT brands to copy. They are reference points for what our voice is trying to do.

**[Saint 1 - brand or writer]:**
Why: [One sentence on what specifically they do that we want to channel]
Not: [One sentence on what they do that we would NOT do]

**[Saint 2]:**
Why: [...]
Not: [...]

**[Saint 3 - optional]:**
Why: [...]
Not: [...]

Examples of well-matched patron saints:
- Basecamp/Hey (Signal vs. Noise blog): direct opinions, no corporate hedging, willing to be contrarian
- The Hustle: conversational, specific numbers, irreverent but not frivolous
- Khe Hy (RadReads): personal specificity, real dollar amounts, owns the uncertainty
- Paul Graham (essays): no wasted words, counterintuitive positions stated as plainly as possible
```

### Output

Save to `.marketing-reports/[slug]/brand-voice.md`. This is a living document - update the DO/DON'T table whenever a new writing decision reveals a pattern.

---

## MODULE C: COMPETITIVE INTELLIGENCE

**Self-contained - run this module independently for quarterly competitor teardowns.**

Competitive intelligence done well answers one question every quarter: "What changed, and should we respond?" It does not produce a static deck that nobody reads after the initial presentation.

Source: Kompyte, Crayon, and Klue methodology; Salesmotion competitive intelligence tool reviews 2026.

### Step 1: Identify the Top 5 Competitors

Select competitors by the accounts you lose to, not by who you think of as competitors.

Check: recent lost-deal notes, G2/Capterra "alternatives" pages, LinkedIn "people also viewed" on your company page.

List them in rank order by frequency of appearance in lost deals (not by funding or size).

### Step 2: Competitor Profile Template

Fill this for each of the 5 competitors:

```markdown
## Competitor: [Name]

**Positioning statement (from their homepage H1):**
[Copy their exact hero headline - this is what they lead with]

**Top 3 messages (from homepage + pricing page):**
1. [Message 1]
2. [Message 2]
3. [Message 3]

**Pricing:**
- Model: [per seat / flat / usage-based / freemium]
- Published price: [$X/month or "custom" or "contact sales"]
- Free tier: [yes/no - describe]
- Trial: [yes/no - describe]

**Recent launches (last 90 days):**
- [Feature or product launched - source + date]
- [Source: their blog, release notes, LinkedIn, ProductHunt]

**Content themes (top 3 topics they publish about):**
1. [Theme]
2. [Theme]
3. [Theme]

**Where they are weak (from G2/Capterra reviews + lost deal notes):**
- [Weakness 1 - with source]
- [Weakness 2 - with source]

**What they say about us (if anything):**
[Battlecard language, comparison pages, or "not mentioned"]
```

### Step 3: Signal Tracking Setup

Set up these five tracking sources. Check them as part of a quarterly review, not daily.

```markdown
## Signal Tracking Setup

**Google Alerts (free):**
- Alert: "[Competitor name]" - weekly digest to a shared inbox or Notion page
- Alert: "[Competitor name] pricing" - any pricing change mentions

**LinkedIn:**
- Follow the company page (posts visible in feed)
- Follow their CMO/CPO/CEO (messaging updates often come from leadership)
- Watch for job postings: if they're hiring for X, they're building in that direction

**G2/Capterra:**
- Save their competitor profile page
- Sort reviews by "most recent" - read the last 5 every quarter
- Note: what changed in what customers complain about vs. last quarter

**Newsletter subscription:**
- Subscribe personally with a non-corporate email
- Tag in your email client: [Competitor CI]
- Read for tone and topic shifts, not just announcement content

**ProductHunt:**
- Search competitor name - see what they've launched and how it was received
- Check upvote count and comments for signal on feature reception
```

### Step 4: Quarterly Intel Report Format

```markdown
## Competitive Intelligence Report - Q[X] [Year]

**Report date:** [Date]
**Prepared by:** [Name or "CI process"]
**Review cadence:** Quarterly - next review: [Date]

### What Changed Since Last Quarter

| Competitor | What Changed | Significance | Our Response (if any) |
|------------|-------------|--------------|----------------------|
| [Name] | [Specific change] | [High/Med/Low] | [Action or "monitor"] |

### Positioning Shifts

[Did any competitor change their hero headline or top message? Note it here with old vs. new.]

### Pricing Movements

[Any pricing page changes? New tiers, removed free tier, pricing increase?]

### New Feature Launches

[Features launched this quarter across all 5 competitors - brief description each]

### Emerging Weak Spots (from review sites)

[Any increase in complaints about a specific area? This is a battlecard opportunity.]

### Battlecard Updates Triggered

[Which sales battlecards need updating based on this quarter's changes?]
```

### Output

Save to `.marketing-reports/[slug]/competitive-intel-Q[X]-[Year].md`. Previous quarters are archived (not deleted) - the "what changed" section requires comparing to prior reports.

---

## MODULE D: EDITORIAL CALENDAR

**Self-contained - run this module independently to build or reset a content operations calendar.**

An editorial calendar fails when it is just a list of content ideas with dates. It succeeds when it enforces content pillars, tracks owner accountability, and connects every piece to a campaign or business goal.

Source: HubSpot blog editorial calendar methodology, Airtable content ops patterns, Content Harmony calendar templates.

### Step 1: Establish Monthly Themes

Themes are campaign-aligned, not topic-based. "Leadership" is a topic. "How we help ops teams stop losing deals to process gaps" is a theme tied to a campaign goal.

```markdown
## Monthly Theme Plan

| Month | Theme | Campaign Alignment | Primary Persona |
|-------|-------|-------------------|----------------|
| [Month] | [Theme - specific, campaign-tied] | [Campaign name from campaign.json] | [Persona name] |
```

### Step 2: Content Pillar Enforcement

Before adding any piece to the calendar, confirm which pillar it serves. If it serves no pillar, it does not go on the calendar.

Use the pillar definitions from Phase 7 of the main marketing pipeline. Every piece on the calendar must be tagged.

### Step 3: The Calendar Template

```markdown
## Editorial Calendar - [Month] [Year]

**Monthly Theme:** [Theme]
**Campaign:** [Campaign name]
**Pillar focus:** [Which content pillar(s) this month emphasizes]

| Date | Content Title | Format | Platform | Pillar | Status | Owner | Deadline | Notes |
|------|--------------|--------|----------|--------|--------|-------|----------|-------|
| [Date] | [Title/topic] | [Blog/Email/LinkedIn/Video] | [Platform] | [Pillar #] | Ideation | [Name] | [Date] | [Link or brief] |

### Status Definitions

- **Ideation:** Topic approved, brief not yet written
- **Draft:** Writing in progress
- **Review:** Submitted for editorial/brand review
- **Scheduled:** Approved and loaded into scheduler or CMS
- **Published:** Live
- **Archived:** Produced but held or deprecated
```

### Step 4: The Repurposing Tree

Every "hero" piece (long-form blog, research report, case study, webinar) spawns derivatives. Document the tree before producing the hero piece so derivative slots are reserved on the calendar.

```markdown
## Repurposing Tree: [Hero Piece Title]

**Hero piece:** [Type + title + target publish date]

Derivatives:
1. LinkedIn post - hook from the key finding (publish same day as hero)
2. X thread - argument broken into 5-6 tweets (publish day 2)
3. Email newsletter - one idea from the piece, fully developed (publish same week)
4. Short-form video script - 60-second version of the core insight (publish week 2)
5. Quote card - the most standalone sentence from the piece (publish any time, evergreen)
6. Repurposed as a FAQ section in a landing page section (ongoing)
7. Pitched as a contributed article angle to industry publication (if research-backed)

**Reserve these calendar slots before writing the hero piece.**
```

### Step 5: Capacity Check

Before finalizing the calendar, run a capacity check. Content that is planned but never produced destroys team trust in the calendar.

```markdown
## Capacity Check

**Total pieces planned:** [N]
**Total person-hours required (estimate):** [N hours]
**Available person-hours this month:** [N hours]

If required > available: cut pieces from the calendar, do not cut quality.
Underpromise and overdeliver on the calendar. A calendar of 8 published pieces beats a calendar of 15 that produced 6.
```

### Output

Save to `.marketing-reports/[slug]/editorial-calendar-[YYYY-MM].md`. Update status column in-place as pieces move through the workflow - this is a live document, not a planning artifact.

---

## MODULE E: CASE STUDY CAPTURE

**Self-contained - run this module to identify, capture, and produce great case studies.**

Most case studies are bad because they start from the wrong place: "We need a case study for our website." Great case studies start from the customer's experience and are captured close to the moment of value delivery - not months later when the story has been sanitized.

Source: Heavybit customer case study methodology, B2B SaaS case study interview frameworks (Concurate, FormAssembly), Edify Content case study methodology.

### Step 1: Identify Candidates

Do not send case study requests to everyone. Target specifically:

```markdown
## Case Study Candidate Criteria

**Minimum threshold for outreach:**
- Customer has been active for 60+ days (enough time for real results)
- Usage data shows meaningful adoption (not just signed up)
- Recent positive interaction: NPS score 8+, support resolved cleanly, renewed, expanded, or left a positive review

**Ideal candidate signals:**
- Customer mentioned a specific result in a support ticket, renewal call, or review
- Customer has a recognizable company name in your target ICP (name-recognition proof point)
- Customer's use case represents a segment you are actively trying to grow

**Do not target:**
- Customers currently in an open support issue
- Customers in a renewal conversation where the case study ask could feel transactional
- Customers who have not seen a result yet (the story isn't there)
```

### Step 2: Outreach Template

The outreach email asks for a specific kind of help, not a generic testimonial.

```markdown
Subject: Quick question about your results with [Product]

[Name],

I noticed [specific observation - e.g., "your team has been using [Feature] heavily this quarter" or "you left us a review mentioning [specific thing]"].

I'd love to capture your story in a short case study - specifically [the metric or outcome you've seen, if known], or whatever results have been most meaningful for your team.

It would be a 15-minute call. I'll send questions in advance so there's no prep required on your side. We'll review anything before it goes public.

Would [Date/Time] work, or is there a better time this week?

[Your name]

---
P.S. If your team would prefer we use your story anonymously or as a composite, that works too - just let me know.
```

Key rule: the email names a specific observation about their usage. "I'd love to feature you in a case study" gets a 5-10% reply rate. "I noticed X and would love to capture that story" gets 30-40%.

### Step 3: Interview Questions (15 Minutes Maximum)

Send these to the customer before the call. A 15-minute call with prepared answers produces better material than a 45-minute call without preparation.

```markdown
## Case Study Interview Questions

**Pre-send note to customer:** "These 7 questions take most people 5-10 minutes to skim before our call. The more specific you can be on numbers - time saved, deals closed, cost reduced - the more compelling the story."

1. **Before:** What were you doing before [Product], and what was the biggest frustration with that approach?

2. **The trigger:** What specifically made you look for a different solution? Was there a moment or event that pushed you to act?

3. **Evaluation:** Did you look at other options? What made you choose [Product] over [alternatives]?

4. **Implementation:** How long did it take to get up and running? What surprised you about the setup process?

5. **Results (quantitative):** What specific metrics have changed since you started using [Product]? Even rough numbers are useful - time saved per week, percentage reduction, number of [X] per month.

6. **Results (qualitative):** Beyond the numbers, how has it changed how your team works or how you feel about [the area the product addresses]?

7. **What's next:** What are you hoping to do with [Product] in the next 6-12 months?
```

Record the call. Use a transcription tool (Descript, Otter.ai, or similar). Take structured notes. The most quotable sentences rarely come from the questions themselves - they come from the natural follow-up conversation.

### Step 4: Case Study Structure

```markdown
## Case Study: [Customer Name] - [One-Line Result]

**Company:** [Name, size, industry]
**Use case:** [What they use the product for - one sentence]
**Result headline:** [The single most compelling number or outcome]

---

### Before
[Describe the specific problem - use the customer's words where possible. What were they doing, why was it painful, what was the cost of not solving it.]

### The Trigger
[What made them act - the specific event or moment. Keep it concrete.]

### Evaluation
[Why they chose you over alternatives. Be specific about what they compared.]

### Implementation
[How long it took. What was harder or easier than expected. Real details build credibility.]

### Results
[Specific numbers first: X% reduction in Y, Z hours saved per week, $N in [outcome].
Then the qualitative shift: what changed about how the team works or feels.
Use the formula: "Reduced [X] by [%], from [old state] to [new state]" - both the percentage and the absolute change tell a more complete story than either alone.]

### What's Next
[Their plans going forward - signals healthy ongoing relationship and future proof points.]

---

**Direct quote (for pull quote use):**
[Best 1-2 sentence quote from the interview - must be verbatim, attributed to name and title]

**Company logo approved for use:** [yes/no]
**Review/approval status:** [Pending / Approved by [Name] on [Date]]
```

### Step 5: Legal Review Checklist

Before publishing:

```markdown
## Publication Checklist

- [ ] Customer has reviewed the draft and approved in writing (email approval is sufficient)
- [ ] Company name and logo use explicitly approved (not assumed from case study approval)
- [ ] All metrics and quotes verified against interview recording or transcript
- [ ] No claims made beyond what the customer confirmed in writing
- [ ] NDA check: does this customer have an NDA that restricts public reference?
- [ ] Logos: do you have high-resolution versions of their logo in the correct format?
- [ ] Attribution: is the spokesperson's name, title, and current company correct?
- [ ] If published on review sites (G2, Capterra): customer notified and agrees to cross-post
```

### Output

Save to `.marketing-reports/[slug]/case-studies/[customer-slug].md`. The legal checklist must be complete before the file is marked published.

---

## MODULE F: BRAND GUIDE GENERATION

**Self-contained - run this module when you have a product URL and need a complete brand guide fast.**

Given a product URL, scrape key pages via `/scrape`, analyze the content for voice, positioning, and visual brand signals, and produce a structured brand guide ready for use by agents and humans.

This module is the fastest path from "here's a URL" to "here's a brand guide." It does not require an interview or campaign setup - just a URL and a target brand name.

### When to Use Module F

- You are onboarding a new product or client and need brand context before producing any assets
- You want a brand guide for a competitor to inform positioning
- You need to align agents across a campaign on a consistent brand voice
- An existing campaign lacks documented voice and style guidance

### Step 1: Scrape Key Pages

Invoke `/scrape` on these pages. Check for existence before adding to the list - not every site has every path.

**Required pages:**
- `[root]` (homepage)
- `/about`

**Check and include if they exist (HTTP 200):**
- `/pricing`
- `/features` or `/product`
- `/customers` or `/case-studies` or `/testimonials`
- `/blog` (homepage only, not individual posts)

**Optional - include if the user wants competitive depth:**
- First 3 blog posts (for voice pattern analysis)

Pass the URL list to `/scrape` with `output_format: markdown`. Use cache (24h TTL). Report which pages were found vs. not found.

```
Typical invocation to /scrape:
  Target: [homepage, /about, /pricing, /features, /customers]
  Output: markdown
  Type: server-rendered (assume unless user flags otherwise)
  Reuse: one-time (no cache setup needed unless user requests repeat runs)
  Auth: none (if auth required, flag it - Module F cannot proceed)
```

### Step 2: Analyze Scraped Content

Read all scraped markdown files. Extract these signals:

**Positioning signals:**
- Hero headline (H1 on homepage) - exact text
- Subhead or supporting line under the hero
- Positioning statement (how they describe what they do and for whom)
- Category claim (what market category do they say they're in?)

**Voice patterns (analyze across all scraped content):**
- Sentence length average (short/punchy vs. long/explanatory)
- Formality level (1-5: 1=very casual, 5=formal enterprise)
- Humor presence (yes/no + examples if yes)
- Technical depth (surface-level vs. jargon-heavy)
- Person (first person "we/our" vs. second person "you/your" vs. neutral)
- Specific vs. vague claims (count specific numbers/stats vs. vague adjectives)

**Visual brand signals (extracted from text and alt text):**
- Color words mentioned in copy (e.g., "dark mode," color names in product descriptions)
- Imagery themes from img alt text (if scraper captures it)
- Design vocabulary used in product descriptions (e.g., "clean," "minimal," "powerful")

**Social proof style:**
- Testimonial format (full quotes, metrics-first, named vs. anonymous)
- Customer logo presence (mentioned in copy or alt text)
- Proof point style (percentages, time savings, revenue, qualitative transformation)

**Pricing tier language:**
- Plan names and language used
- Key feature gate signals
- CTA language on pricing page (e.g., "Start free" vs. "Book a demo" vs. "Contact sales")

**Competitive positioning:**
- Explicit competitor mentions
- "Unlike [X]" or "vs. [X]" language
- Who they compare themselves to or position against

### Step 3: Generate Outputs

Produce four files in `.marketing-reports/YYYYMMDD-brand-[product]/` (or `~/.claude/marketing-global/brand-[product]/` if user chooses global storage):

#### brand-[product].json

Structured data for agent consumption. Other skills read this file to get brand context without re-analyzing.

```json
{
  "brand_name": "[name]",
  "url": "[homepage URL]",
  "scraped_at": "[ISO timestamp]",
  "hero_headline": "[exact H1 text]",
  "positioning_statement": "[how they describe themselves]",
  "category": "[market category they claim]",
  "icp_signals": "[who they say they serve]",
  "voice": {
    "adjectives": ["[adj1]", "[adj2]", "[adj3]"],
    "formality": [1-5],
    "sentence_style": "short-punchy | medium-balanced | long-explanatory",
    "humor": true,
    "technical_depth": "surface | moderate | jargon-heavy",
    "person": "first | second | mixed"
  },
  "proof_style": "[metrics-first | quote-first | logo-forward | anonymous]",
  "pricing_model": "[per-seat | flat | usage | freemium | contact-sales]",
  "cta_language": ["[CTA 1]", "[CTA 2]"],
  "competitor_mentions": ["[comp1]", "[comp2]"],
  "avoid": ["[pattern to avoid based on their style]"]
}
```

#### brand-guide-[product].md

Human-readable brand guide. Written so any person or agent can pick it up and write in-brand immediately.

Structure:
1. Brand overview (name, positioning, what they do, who for)
2. Voice and tone (3 adjectives with concrete meaning, DO/DON'T table - minimum 8 rows extracted from actual content)
3. Key messages (hero message, 3 supporting messages pulled from their copy)
4. Social proof approach (how they prove their claims, what format they use)
5. Pricing language (how they describe tiers and value)
6. Competitive context (who they compete with, how they position vs. them)

#### voice-analysis.md

A focused DO/DON'T table built entirely from patterns found in the scraped content - not invented, but extracted.

```markdown
# Voice Analysis: [Brand]

Source pages: [list of scraped pages]
Analyzed: [timestamp]

## Extracted DO / DON'T Table

| DO (they say this) | DON'T (they avoid this) | Pattern |
|--------------------|------------------------|---------|
| [actual phrase from their copy] | [contrasting approach they don't use] | [what this reveals] |

## Voice Fingerprint

**Sentence rhythm:** [description with example]
**Specificity level:** [X% of claims have specific numbers] (found [N] stats across [N] pages)
**Formality score:** [X/5] - [what this means in practice]
**Most distinctive patterns:** [list 2-3 things that are distinctively theirs]
```

#### competitive-map.md

Who they position against and how.

```markdown
# Competitive Map: [Brand]

## Explicit Mentions

| Competitor | Context | How [Brand] Positions vs. Them |
|------------|---------|-------------------------------|
| [name] | [page and quote where mentioned] | [their positioning angle] |

## Implicit Positioning

[Patterns in language suggesting who they're writing against, even without naming them]

## Category Claim

[What market category they say they're in, and what that implies about their competitive frame]

## Gaps and Opportunities

[What competitors they don't mention, what angles they leave open]
```

### Storage Decision

At the end of Module F, ask:

```
Save as:
  A) Project-local: .marketing-reports/YYYYMMDD-brand-[product]/
     (only available in this project)
  B) Global: ~/.claude/marketing-global/brand-[product]/
     (reusable across all projects - recommended for your own brand or recurring clients)
```

If the user chooses global, also write the compact `brand-[product].json` to `~/.claude/marketing-global/` so `/marketing` can find it at startup.

### Module F Output

```
.marketing-reports/YYYYMMDD-brand-[product]/
  brand-[product].json          (structured data for agents)
  brand-guide-[product].md      (human-readable guide)
  voice-analysis.md             (DO/DON'T table from real content)
  competitive-map.md            (who they compete with and how)
  scraped/                      (raw scraped pages from /scrape)
    [url-slug].md               (one per scraped page)
```

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — Generic marketing campaign pipeline, works for any product/industry -->
<!-- Part of the Claude Code Skills Collection -->
<!-- License: MIT -->
