---
description: "/copy — Conversion copywriting for any asset: landing pages, emails, ads, onboarding flows, sales pages, CTAs. Any product, any audience, conversion-optimized output."
allowed-tools: Bash(git *), Bash(mkdir *), Bash(ls *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, Skill, WebFetch, WebSearch
---

# /copy — Conversion Copywriting Pipeline

**Steel Principle: Every piece of copy must have a defined reader, a single job to do, and a clear next action. Copy that tries to do everything converts nobody.**

Produces conversion-focused copy for any asset type: landing pages, email sequences, ad copy, onboarding flows, sales pages, CTAs, pricing pages, product descriptions, and more. Works for any product, service, or audience.

**FIRE AND FORGET** — Specify the asset, the product, and who it's for. The skill handles structure, messaging, and variants.

> **When to use /copy:**
> - Writing a landing page, hero section, or full sales page
> - Drafting an email or email sequence (welcome, nurture, sales, re-engagement)
> - Writing ad copy (search ads, social ads, display)
> - Creating onboarding copy (tooltips, empty states, activation flows)
> - Writing CTAs, buttons, and microcopy
> - Auditing and rewriting existing copy that isn't converting
>
> **When NOT to use /copy:**
> - You need a full marketing strategy - use `/marketing` first to build the messaging framework, then return here
> - You need social media posts - use `/social` instead
> - You need a blog article - use `/blog` instead

---

## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** Copy is never written without a defined reader (ICP), a stated job (what action should the reader take), and a message angle (what pain/desire is being addressed)
- **Steel Principle #2:** Every deliverable includes at minimum two variants - copy without a test variant is an assumption, not a strategy
- **Steel Principle #3:** Every claim must be supportable - fabricated statistics, unverifiable testimonials, or empty superlatives ("the best," "world-class") are prohibited; flag placeholders clearly

### Copy-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The reader will figure out what to do, the CTA isn't the most important part" | Unclear CTAs are the #1 cause of page abandonment; visitors need to know exactly what happens when they click | Write the CTA first, then write the copy that earns it |
| "We can add proof points later, the copy is good" | Copy without proof is opinion; readers don't trust claims without evidence | Use [PLACEHOLDER: stat] or [PLACEHOLDER: testimonial] to mark where proof is needed; never pretend evidence exists |
| "Long copy doesn't convert, keep it short" | Short copy converts for low-consideration purchases; high-consideration products need full argument development | Match copy length to decision complexity, not to personal preference |
| "Features are benefits, just list them" | Features describe the product; benefits describe what the reader's life looks like after using it | For every feature, complete: "which means you can..." or "so you no longer have to..." |
| "The headline is fine, let's focus on the body" | 80% of readers read the headline and nothing else; a weak headline means the body is unread | Write 5-10 headline options minimum; never ship the first attempt |
| "We'll A/B test later" | "Later" is never scheduled; build two variants from the start, then testing is already done | Produce Variant A and Variant B in the same delivery |

---

## STATUS UPDATES

> Reference: [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)

```
✍️  /copy Started
   Asset type:  [landing page / email / ad / etc.]
   Product:     [product name]
   Reader:      [ICP / target audience]
   Goal:        [the one action the copy must drive]

📋 Phase 1: Brief Intake
   ├─ Asset type confirmed
   ├─ Reader defined
   └─ Message angle selected

✏️  Phase 2: Copy Production
   ├─ Headline variants: [count]
   ├─ Body copy: [sections / word count]
   └─ Variants: [A/B generated]

🔍 Phase 3: Self-Review
   ├─ Clarity check
   ├─ Proof point audit
   └─ ✅ CTA strength confirmed

✅ Done
   Output saved to: .copy-reports/[slug]/
```

---

## OUTPUT STRUCTURE

```
.copy-reports/
├── [asset-slug]/
│   ├── BRIEF.md                # Copy brief (reader, job, angle, constraints)
│   ├── [asset-name]-A.md       # Primary variant
│   ├── [asset-name]-B.md       # Alternate variant
│   ├── headlines.md            # All headline options (scored)
│   └── notes.md                # Proof point placeholders, open questions
```

---

## PHASE 0: INTAKE

Parse the user's request. Gather before writing:

**Required - do not skip:**
- **Asset type:** What format? (landing page, email, ad headline, CTA, onboarding email, pricing page)
- **Product/service:** What does it do? What problem does it solve?
- **Reader:** Who is this copy written for? Role, situation, pain, level of awareness
- **Job:** What is the single action this copy must drive? (sign up, book demo, buy, upgrade, reply)
- **Message angle:** Pain-led, outcome-led, or social proof-led?

**Optional but useful:**
- Existing copy to rewrite (if rewrite, read it before writing a single word)
- Tone/voice notes
- Constraints (character limits, required legal disclaimers, required phrases)
- Proof points available (stats, testimonials, case studies)
- Competitor context (what are they saying that we need to differentiate from)

If asset type, reader, and job are not all present, ask for them. Do not write a word until these three are defined.

**Setup:**
```bash
mkdir -p .copy-reports
grep -q ".copy-reports" .gitignore 2>/dev/null || echo ".copy-reports/" >> .gitignore
```

**Brand context lookup (before building the copy brief):**
1. Check `.marketing-reports/brand.json` in the current project - if found, pre-populate the ICP, voice, and proof points from it
2. If not found, check `~/.claude/marketing-global/` for named brand files: `ls ~/.claude/marketing-global/ 2>/dev/null`
3. If global brands exist, present options: "Found global brands: [list]. Use one as the brief foundation, or define fresh?"
4. Loaded brand context pre-fills: reader, voice, proof points, and avoid-list; user confirms or overrides
5. After new brief is built, offer: "Save this as a reusable brand context? [global / project-local / skip]"

---

## PHASE 1: COPY BRIEF

Build and confirm a one-page copy brief before writing:

```markdown
## Copy Brief: [Asset Name]

**Asset type:** [landing page / email / ad / etc.]
**Product:** [product name + one sentence on what it does]
**Reader:** [who they are, what they're struggling with, what they want]
**Awareness level:** [unaware / problem-aware / solution-aware / product-aware / most aware]
**Job this copy must do:** [single action]
**Message angle:** [pain-led / outcome-led / proof-led]
**Tone:** [professional / casual / technical / empathetic / urgent / etc.]
**Length target:** [headline only / ~200 words / ~500 words / full page]
**CTA:** [exact CTA text, or ask user]
**Proof points available:** [list or "none - use placeholders"]
**Constraints:** [character limits, required phrases, legal notes]
**Competing messages to avoid:** [what the competition is saying]
```

**Awareness level definitions:**
- **Unaware:** Doesn't know they have the problem - needs education before solution
- **Problem-aware:** Knows the pain but not that solutions exist - name the pain, then present category
- **Solution-aware:** Knows solutions exist but hasn't chosen one - differentiate
- **Product-aware:** Knows your product but hasn't bought - address objections, reduce friction
- **Most aware:** Ready to buy, just needs the offer - be direct, show value, easy CTA

This determines how much education the copy needs to do before pitching.

Present the brief, confirm with user, then write. Do not write before confirmation.

---

## PHASE 2: COPY PRODUCTION

### 2.1 Headline Writing

Write 5-10 headline options before selecting. Headlines must do ONE of:
- Address the primary pain directly: "Stop losing clients to 'can you send me an update' emails"
- State the primary outcome: "Client projects that run on autopilot - and they know it"
- Make a specific promise: "Cut project status meetings by 80% in 30 days"
- Lead with social proof: "How 200 freelancers handle 5x more clients without working more hours"
- Challenge a false belief: "You don't need a bigger team. You need a better client system."

**Score each headline 1-3 on:**
- Specificity (1=vague, 3=very specific)
- Pain/desire resonance (1=generic, 3=strikes a nerve)
- Clarity (1=requires explanation, 3=instantly understood)

Select the top scorer. Include the top 3 in the deliverable. The user can choose.

**Bad headlines (do not produce):**
- "[Product] - The [adjective] [category] for [audience]" (generic positioning template)
- "Welcome to [product name]" (says nothing)
- "Introducing [feature name]" (feature, not benefit)
- Anything with "world-class," "best-in-class," "cutting-edge," or "innovative"
- "In today's [X], [Y] is more important than ever" - banned outright
- Any headline that could have been written about any company in any category without changing a word - banned; if the company name and product category are the only differentiators, it's not a headline, it's a placeholder

### 2.2 Copy Structures by Asset Type

#### Landing Page / Hero Section

```
[Headline - addresses pain or outcome]
[Subhead - elaborates on headline, adds proof or specificity]

[CTA button - action verb + outcome, not "Submit" or "Click here"]

[Optional: social proof bar - logos, stats, review count]

---

[Value propositions - 3 columns or bullets]
[Each: Icon + Bold benefit headline + 1-2 sentence explanation]

---

[How it works - 3 steps max]
[Numbered, short, outcome-focused at each step]

---

[Social proof - testimonials with name, title, company]
[Use real or [PLACEHOLDER: testimonial from [ICP type]] clearly marked]

---

[Objection-handling section]
[Address the top 3 objections as FAQ or paragraph]

---

[Final CTA - repeat with more urgency or specificity]
```

#### Email - Welcome / Activation

```
Subject line: [3 options - curiosity / direct / social proof variants]
Preview text: [extends subject, 40-90 chars]

---

[Opening line: acknowledge who they are or what they just did]
[1 paragraph: the transformation this product enables]
[1-2 paragraphs: the first action you want them to take + why it matters]
[CTA: specific, single action]
[P.S.: optional - adds urgency or addresses top objection]
```

#### Email - Nurture / Value

```
Subject line: [3 options]
Preview text: [3 matching options]

---

[Hook: a specific pain, insight, or story that opens a loop]
[Value: the insight, tip, or story - 150-300 words]
[Bridge: how this connects to the product]
[Soft CTA: low friction, not hard sell]
```

#### Email - Sales / Conversion

```
Subject line: [3 options - direct offer, curiosity, urgency]
Preview text: [3 matching options]

---

[Opening: specific pain state]
[Stakes: cost of not solving this]
[Solution: product presented as the answer]
[How it works: 3 bullets]
[Proof: stat or testimonial [PLACEHOLDER if none]]
[Objection handling: 2-3 common objections addressed]
[Offer: what they get, price if applicable]
[CTA: clear, single action, deadline if real]
[P.S.: repeat CTA or strongest proof point]
```

#### Ad Copy - Search (Google/Bing)

```
Headlines (15 variations, max 30 chars each):
- Pain-led: [5 options]
- Outcome-led: [5 options]
- CTA/offer-led: [5 options]

Descriptions (4 variations, max 90 chars each):
- Pain + solution + CTA: [2 options]
- Proof + offer + CTA: [2 options]
```

#### Ad Copy - Social (paid social)

```
Primary text (3 variants):
- Variant A (pain-led): [200-500 chars]
- Variant B (outcome-led): [200-500 chars]
- Variant C (proof-led): [200-500 chars]

Headline (3 variants, ~25-40 chars each)
Description (3 variants, ~25-30 chars each)
CTA button: [select from platform options]
```

#### CTA Copy

Write 5 options. Each must:
- Start with an action verb (Get, Start, Try, See, Join, Book, Unlock)
- Include an outcome or specificity (not just "Submit" or "Click here")
- Match the friction level of the ask (low friction = "See how it works"; high friction = "Start free trial")

Examples of good CTA copy:
- "Get my free account" (ownership language)
- "See a 5-minute demo" (low commitment, specific)
- "Start cutting status emails today" (outcome-specific)
- "Try it free for 14 days" (risk reducer in the CTA)

#### Onboarding Copy (tooltips, empty states, activation prompts)

```
For each screen/state:
- [State name: e.g., "Empty dashboard - no projects yet"]
- Headline: [what to do, not what's missing]
- Body: [why this is the first step + what happens after]
- CTA: [action verb + specific action]
```

Onboarding copy rule: never describe the absence of data. Describe the first action that creates value.
- Bad: "You don't have any projects yet."
- Good: "Create your first project to start tracking client work in one place."

### 2.3 Proof Point Protocol

For every claim that requires evidence:

If proof is available: use it exactly as provided. Do not embellish.

If proof is NOT available: write the copy with a clearly marked placeholder:
- `[PLACEHOLDER: customer quote from a [ICP role]]`
- `[PLACEHOLDER: stat - e.g., "X% of users report [outcome]"]`
- `[PLACEHOLDER: case study - [company type] went from X to Y]`

Never invent statistics. Never write a testimonial and pass it off as real. Placeholders are honest; fabrications destroy trust.

### 2.4 Variant Generation

Produce two variants for every deliverable:

- **Variant A (Primary):** Uses the approved message angle
- **Variant B (Alt):** Flips the angle (if A is pain-led, B is outcome-led; if A is long, B is short-form)

Label clearly. Present both. Let the user decide which to test first.

### 2.5 Framework: Honest Observation

Use this framework when the brief calls for brand trust, thought leadership, or email/social copy that earns attention before selling. It works because it names something the reader has experienced but never articulated.

**Structure:**

1. State a thing that is true and the reader has experienced but has not named
2. Explain why it is true (one specific example - not a list)
3. Connect to the product or service without forcing the bridge

**Example:**
> "Most marketing advice tells you what to do. Almost none tells you when to stop.
>
> We spent eight months running the same email sequence because it 'worked' in Q1. By Q3 it was burning the list. Nobody flagged it because it was still technically converting.
>
> [Product] tracks reply rate and forward rate - the signals that tell you a sequence is fatiguing before it shows up in unsubscribes."

The first sentence names the truth. The second grounds it in a real event. The third earns the right to mention the product.

Do not force-fit this framework onto every asset. Use it for nurture emails, LinkedIn thought leadership posts, and brand copy where trust must precede conversion.

---

## PHASE 3: SELF-REVIEW

Before delivering, check every asset against this list:

**Clarity:**
- [ ] Would a 14-year-old understand what this product does from reading the headline?
- [ ] Is the CTA unambiguous about what happens when you click?
- [ ] Are there any jargon terms that require insider knowledge?

**Conversion fundamentals:**
- [ ] Does the headline address the reader's pain or desired outcome (not the company's features)?
- [ ] Is there exactly ONE primary CTA per asset?
- [ ] Are the top 1-2 objections addressed somewhere in the copy?
- [ ] Is there at least one proof point (or a clearly marked placeholder)?

**Proof integrity:**
- [ ] Every statistic is either provided by the user or marked [PLACEHOLDER]
- [ ] Every testimonial is either provided by the user or marked [PLACEHOLDER]
- [ ] No superlatives ("best," "world-class") without supporting evidence

**Variants:**
- [ ] Variant A and Variant B are both present
- [ ] Variants genuinely differ in angle, not just word choices

**AI-slop audit - scan every sentence against these 30 patterns; rewrite any match:**

1. "In today's fast-paced world..."
2. "Let's dive in"
3. "Unpack this"
4. "It's more important than ever"
5. "Game-changer"
6. "Cutting-edge"
7. "Landscape" as a noun ("the marketing landscape")
8. "Navigating [noun]" as a headline
9. "At the end of the day..."
10. "It's not just about X, it's about Y"
11. Three-word subheadings ending in "Matters"
12. Numbered lists of exactly five or ten items
13. "The key takeaway here is..."
14. Opening with an unasked rhetorical question
15. "In conclusion" / "To summarize"
16. Uniform sentence-length rhythm throughout
17. "Harness the power of..."
18. "Leverage [noun] to [verb]"
19. "Empower your team to..."
20. "Unlock [benefit]" as a standalone hook
21. Symmetrical paragraph structure
22. "Furthermore," "Moreover," "Additionally"
23. "Thought leadership" as self-description
24. Emoji structural signposts
25. Hook where one word swap makes it apply to any other brand
26. "The truth is..." followed by an obvious thing
27. Closing "What would you add?" when the content didn't earn a response
28. Fable-structure stories (no mess, no ambiguity, clean arc)
29. Universally-true advice ("communicate clearly")
30. Stats with no source, year, or methodology

**Voice test:**
- [ ] Read the first 3 lines out loud. If you would be embarrassed for a sharp, skeptical reader to think you wrote this, rewrite it. Do not proceed until you pass this test.

Fix any failures before delivering. Do not present copy that fails this checklist.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Copy-Specific Cleanup

1. All copy assets saved to `.copy-reports/[slug]/` - already gitignored
2. No servers, browsers, or APIs opened - nothing to close
3. If WebFetch was used for competitor research, stateless - no cleanup needed
4. BRIEF.md and notes.md are the paper trail - keep them alongside the copy

---

## SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```
═══════════════════════════════════════════════════════════════
SITREP — COPY DELIVERED
═══════════════════════════════════════════════════════════════

  Asset:           [asset type + name]
  Product:         [product name]
  Reader:          [ICP one-liner]
  Job:             [action the copy drives]
  Message Angle:   [pain-led / outcome-led / proof-led]

  Deliverables:
    Variant A:     [headline preview] → [CTA]
    Variant B:     [headline preview] → [CTA]
    Headlines:     [count] options (top 3 included)

  Proof Points:    [count provided / count placeholdered]
  Self-Review:     PASSED / [flags to address]

  Files:           .copy-reports/[slug]/

═══════════════════════════════════════════════════════════════
                    Next: Choose a variant to test first
═══════════════════════════════════════════════════════════════
```

---

## RELATED SKILLS

**Feeds from:**
- `/marketing` - the approved messaging framework (ICP, positioning, Tier 1/2/3 messages) must be loaded before writing any asset; copy without a strategy produces inconsistent messaging
- `/brainstorm` - if the asset is for a new concept without strategy yet, brainstorm the angle first (Socratic exploration, HARD-GATE before writing)
- `/design` - when the asset lives inside a component system, design output constrains copy length and tone

**Feeds into:**
- `/blog` - landing page copy angles and hook copy can be adapted into article intros and section headers
- `/social` - CTA copy and hook copy produced here feeds directly into social post hooks
- `/subagent-dev` - for multi-page copy batches (full funnel), dispatch each asset as a reviewed task

**Pairs with:**
- `/marketing` - run marketing first for strategy, then copy for the specific asset production
- `/design` - copy and design should be developed in parallel; copy informs component layout, design constrains copy length

**Auto-suggest after completion:**
When this skill finishes successfully, suggest: `/social` to adapt the key messages into platform-native posts, or `/blog` if the topic warrants a full article

---

## REMEMBER

- **Brief before writing** - undefined reader + job = wasted copy
- **Headline first** - write 5-10 options minimum; never ship the first attempt
- **Features -> benefits -> outcomes** - always complete the "which means you can..." chain
- **Proof or placeholder** - never fabricate evidence; mark gaps clearly
- **Two variants minimum** - every deliverable ships testable
- **One CTA per asset** - asking readers to do three things means they do nothing
- **Match length to decision complexity** - low-stakes = short; high-stakes = full argument

---

## APPENDIX A: LANDING PAGE TEARDOWN CHECKLIST

Run this checklist on any landing page before declaring copy complete. It is also the checklist to run when auditing an existing page that is underperforming.

Source: Unbounce 49-point checklist, LocaliQ 15-point checklist, Grooic CRO checklist, fibr.ai CRO best practices 2025.

```markdown
## Landing Page Teardown: [Page URL or Name]

### Above the Fold (first viewport - no scrolling)
- [ ] 1. Headline states a specific outcome or removes a specific pain. It does NOT describe the company or category. ("Stop losing deals to 'I need to think about it'" vs. "The Modern CRM")
- [ ] 2. Subheadline adds specificity the headline left out - not a synonym for the headline
- [ ] 3. Primary CTA is visible without scrolling, uses an action verb + outcome ("Get my free account" not "Submit")
- [ ] 4. Hero section passes the 5-second test: a stranger can say what the product does and who it's for within 5 seconds of landing

### Trust and Proof
- [ ] 5. Social proof appears within the first scroll (logos, review count, or a specific stat)
- [ ] 6. At least one testimonial includes a full name, title, and company - not just a first name
- [ ] 7. Testimonials speak to outcomes, not just sentiment ("Reduced our onboarding calls by 60%" not "Great product, highly recommend")
- [ ] 8. If claiming a number (customers, ratings, NPS score) - is it current and verifiable?

### Conversion Mechanics
- [ ] 9. There is exactly ONE primary CTA across the page (other CTAs are the same ask, not different asks)
- [ ] 10. The top 2-3 objections are addressed somewhere on the page (FAQ, or objection-handling section)
- [ ] 11. Risk reducer is present near the primary CTA (no credit card, cancel anytime, free trial, money-back guarantee)
- [ ] 12. Form fields (if a form exists): each field is justified. Remove any field that is not required to deliver the next step.

### Copy Quality
- [ ] 13. Every feature claim completes the "which means you can..." test. Features are described as benefits and outcomes, not specifications.
- [ ] 14. No claims use words that require evidence without providing it: "best," "most," "leading," "enterprise-grade," "world-class"
- [ ] 15. The page passes the ICP substitution test: swap in your competitor's name. If the copy still makes sense, it is not differentiated enough.

### Technical
- [ ] 16. Page load time under 3 seconds (use PageSpeed Insights - every 1-second delay reduces conversions up to 7%)
- [ ] 17. Mobile: primary CTA is above the fold on a 375px viewport (iPhone SE)
- [ ] 18. All links work. No broken images. No placeholder text left in.

### Verdict
- PASSES: [N of 18 checks]
- CRITICAL failures (blocks launch): [list]
- MEDIUM failures (fix before next sprint): [list]
```

---

## APPENDIX B: EMAIL DELIVERABILITY RULES

Apply these before sending any cold or commercial email. Source: Folderly 2025 spam trigger guide, Twilio SendGrid subject line research, Mailchimp spam trigger documentation.

### Subject Line Rules

**Length:** 2-4 words performs best for open rate (Twilio SendGrid data). 6 words is the average. Anything over 9 words gets clipped on mobile.

**Casing:** Title Case and Sentence case both perform. ALL CAPS kills deliverability. Mixed CaPs looks like spam.

**Punctuation:** One question mark maximum. Exclamation points in subject lines are a spam signal. Zero exclamation points is safer.

**Emojis:** 1-2 used naturally do not hurt. 3+ or emojis substituting for words trigger filters.

### Spam Trigger Word Categories

Modern spam filters use machine learning and evaluate context, not just keywords. However, these categories reliably trigger filtering:

| Category | Examples to Avoid |
|----------|------------------|
| Money + pressure | "Act now," "Limited time offer," "Don't miss out," "Once in a lifetime" |
| Financial claims | "Earn cash," "Make money fast," "Guaranteed income," "No investment required" |
| Urgency fabrication | "Only 24 hours left," "Urgent response needed," "Respond immediately" |
| Risk-free claims | "Risk-free," "100% free," "No hidden fees," "Nothing to lose" |
| Credential inflation | "As seen on," "Award-winning," "Best in class," "#1 rated" (without proof) |
| Medical/legal | Any health claim language (regulated industry triggers) |

**Context rule:** "Free" in a subject line is only a trigger when paired with money/urgency language. "Free guide," "Free trial" in isolation are lower risk than "FREE MONEY ACT NOW."

### Deliverability Infrastructure (Non-Negotiable)

Before any cold email campaign:

```markdown
## Deliverability Checklist

- [ ] SPF record set up on the sending domain
- [ ] DKIM record set up and verified
- [ ] DMARC record configured (p=none to start, escalate to p=reject)
- [ ] Sending domain is NOT the primary business domain (use a subdomain: mail.yourdomain.com or outreach.yourdomain.com - protects primary domain reputation)
- [ ] Email warming completed: 2-4 weeks of low-volume sending before full campaign
- [ ] List verified: zero-bounce or equivalent run before first send
- [ ] Unsubscribe mechanism in place (legal requirement in most jurisdictions)
- [ ] Plain text version exists alongside HTML version
```

---

## APPENDIX C: CTA COPY RESEARCH

What the data says about button copy. Source: ContentVerve CTA testing, VWO high-converting CTA button research, KlientBoost CTA analysis, Unbounce conversion optimization data.

### Key Research Findings

**First-person outperforms second-person by up to 90%:**
ContentVerve tested "Start my free 30-day trial" vs. "Start your free 30-day trial." First-person version saw a 90% CTR increase. Subsequent tests by other researchers show 10-40% improvement range. Use "my" not "your" in CTA copy.

**Specificity beats generic by 161%:**
"Get started" vs. "Get my free account" - the specific version outperforms by up to 161% (Unbounce data). The more specific the CTA, the less cognitive friction at the click point.

**Personalized CTAs outperform generic by 202%:**
Dynamic CTAs that adapt to the visitor's context (returning vs. new, read a specific page) convert at 202% of static CTAs. Worth implementing if your CMS or landing page tool supports it.

### CTA Copy Decision Framework

Use this decision tree to select the right CTA type:

```
What is the ask?
├── Low friction (under 2 minutes, no payment)
│   → "See how it works" / "Watch a 3-minute demo" / "Get the free guide"
│   Characteristics: specific time commitment or deliverable
├── Medium friction (signup, account creation, no credit card)
│   → "Start my free trial" / "Create my free account" / "Get instant access"
│   Characteristics: first-person + outcome + risk reducer if possible
├── High friction (payment, demo call, sales conversation)
│   → "Start my 14-day free trial - no credit card" / "Book a 15-minute call"
│   Characteristics: explicit risk reducer next to or in the CTA
└── Re-engagement (visitor has been before, knows the product)
    → "Pick up where I left off" / "Complete my setup" / "Resume my trial"
    Characteristics: continuity language, acknowledges prior context
```

### Words That Convert (Use More)

Research-backed positive signals in CTA copy:
- "Free" (when combined with a specific thing, not a vague promise)
- "Instant" (reduces time anxiety - "Get instant access")
- "Now" (urgency without fabrication - "Start now")
- "My" / "Me" (first-person ownership)
- "Try" (lower commitment than "buy" - "Try it free")
- Specific time durations ("14-day," "30-day," "5-minute")
- Specific actions ("See," "Get," "Start," "Book" - not "Submit" or "Click")

### Words That Hurt (Avoid in CTAs)

- "Submit" (no outcome, pure mechanical action)
- "Click here" (states the mechanics, not the benefit)
- "Learn more" (vague - what am I learning, and why now?)
- "Continue" (presupposes they were going somewhere - only use mid-flow)
- "Enter" (feels like a portal, not an action)
- Any word that could be on any button on any website in any category

---

## APPENDIX D: PRICING PAGE PATTERNS

Source: Orbix Studio SaaS pricing page psychology research, GetMonetizely anchoring effect data, RevenueCat subscription pricing psychology, Winsomemarketng SaaS pricing conversion analysis.

### Pattern 1: Anchoring (The Enterprise Tier)

Present your highest-priced tier first (or most prominently). It recalibrates what "expensive" means.

Research: Slack adding an Enterprise plan at $500/month increased Professional tier ($99) conversions by 28% - with zero feature changes. Visitors anchored to Enterprise pricing, making Professional feel like exceptional value.

Implementation: If you have an enterprise or custom tier, display it. Even "Contact us for pricing" as the rightmost tier anchors the middle option.

Rule: The anchor tier must be real - "Enterprise - Call for pricing" with no further definition is transparent and loses credibility. Give it real feature descriptions.

### Pattern 2: Decoy Pricing (The Middle Trap)

The three-tier structure is specifically designed to funnel users to the middle tier. The bottom tier (basic) makes middle look full-featured. The top tier (pro/enterprise) makes middle look affordable.

Research: Adobe's single-app plan serves as a decoy - it makes the All Apps plan look like exceptional value, increasing All Apps subscriptions by 43% (Adobe 2022).

Implementation: Your middle tier should have the core features 80% of buyers need. Bottom tier is intentionally limited in ways that matter. Top tier is intentionally expensive or restricted for solo use.

Warning: This only works if the feature differences between tiers are meaningful. If the only difference is a seat count, buyers will choose the cheapest tier and upgrade later (or not).

### Pattern 3: Annual vs. Monthly Framing

Never show the annual price as an annual total. Always show it as a monthly equivalent.

Research: Mailchimp showing "$29.99/month, billed annually" instead of "$359.88/year" increased annual plan selection by 21%. Webflow's approach (show annual/month by default, monthly as a toggle) increased annual plan selection by 49%.

Standard implementation:
```
[Monthly toggle]   [Annual toggle - default, shows "Save 20%"]

Pro Plan
$49/month         $39/month
                  billed $468/year
```

The annual plan is the default view. Monthly is available but not the anchor.

### Pattern 4: Highlighted Plan (Recommended)

Visually distinguish the tier you want most buyers to choose. Use a label: "Most popular" or "Recommended" plus a color/border difference.

Research: "Most popular" badges increase conversion to that tier by 20-35% (Orbix Studio data). The label signals social proof without a testimonial.

Rule: Only use "Most popular" if it is actually the most-sold tier. Buyers can tell when this is manufactured and it destroys trust.

### Pattern 5: Friction Reduction Near the CTA

The highest-converting pricing pages place a risk reducer directly below or inside the primary CTA:

```
[Start my free trial]
No credit card required. Cancel anytime.
```

Place this within 1-2 lines of the button. The reassurance must be visible at the moment of decision, not buried in FAQ.

### Pricing Page Self-Review Checklist

Before delivering pricing page copy:
- [ ] Annual plan is the default view
- [ ] Monthly equivalent shown (not annual total)
- [ ] Middle tier is visually distinguished ("Most popular")
- [ ] Anchor tier exists and has real feature description
- [ ] Risk reducer is adjacent to primary CTA
- [ ] Feature differences between tiers are specific and meaningful (not just seat count)
- [ ] FAQ addresses "What happens at the end of my trial?" and "Can I change plans later?"
- [ ] Enterprise/custom tier has a clear path (form, email, or calendar link) - not a dead end

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — Generic conversion copywriting pipeline, works for any product/asset/audience -->
<!-- Part of the Claude Code Skills Collection -->
<!-- License: MIT -->
