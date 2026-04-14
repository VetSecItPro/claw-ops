---
description: "/newsletter — Newsletter-first operator skill. Structure issues, develop voice, build list, repurpose to social. Based on 2026 patterns from Lenny Rachitsky, Packy McCormick, Ben Tossell, Khe Hy."
allowed-tools: Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /newsletter — Newsletter Publishing Pipeline

**Steel Principle: No issue ships without a named reader, a single fully-developed idea, and a subject line that is specific rather than clever.**

A newsletter-first publishing skill. Give it a topic, an existing list, or just a blank page and a goal. It returns positioning, a voice fingerprint, a reusable structure template, and a fully-drafted issue - ready to send.

> **When to use /newsletter:**
> - Writing or improving a recurring email newsletter
> - Building the structure and voice system for a new newsletter from scratch
> - Drafting this week's issue
> - Creating a welcome sequence for new subscribers
> - Planning the next 3 issues in advance
>
> **When NOT to use /newsletter:**
> - You need a landing page or sales email - use `/copy` instead
> - You need social posts for today - use `/social` instead
> - You need a full marketing campaign - use `/marketing` instead

---

## Newsletter Operators Worth Studying

These operators defined 2026 patterns. Each has a distinct structural signature.

**Lenny Rachitsky** (lennysnewsletter.com)
- Practitioner-sourced, not opinion-sourced. "I asked 30 PMs about X" not "here's what PMs should do."
- Synthesizer not guru. Tables, comparisons, point-counterpoint.
- Pattern: Survey or interview data -> structured synthesis -> recommendation with caveats.
- Signature move: Names the range ("some said X, others said Y, the pattern was Z").

**Packy McCormick** (notboring.co)
- 3,000-8,000 word essays with full narrative arcs. Long is a feature, not a bug.
- Opens with a scene or cultural moment, connects to a business thesis.
- Pattern: Hook scene -> background/context (history, market) -> argument -> so-what for reader.
- Signature move: Historical framing that makes a current trend feel inevitable in retrospect.

**Ben Tossell** (bensbites.co)
- Curation-first. Voice is in WHAT you choose to include and the one-sentence commentary.
- 100k+ subscribers on pure curation + brief POV.
- Pattern: Category headers -> curated items with 1-2 sentence take -> short closing thought.
- Signature move: The brief commentary is never neutral. Opinion in every link.

**Khe Hy** (RadReads)
- Productivity, money, and life design. Specific dollar amounts, specific decisions.
- "Here's what I did" not "here's what you should do."
- Pattern: Personal decision or moment -> honest accounting (numbers, tradeoffs) -> framework that generalizes.
- Signature move: Names exact dollar amounts, time durations, and emotional states.

**Morgan Housel** (collabfund.com/blog)
- Opens with a historical anecdote, connects to a timeless behavioral principle.
- Dense paragraphs that earn re-reading. Short pieces, no wasted sentences.
- Pattern: Historical scene (specific time/place/person) -> principle extracted -> modern application.
- Signature move: The historical anecdote IS the argument. The principle arrives naturally.

---

## Anti-Patterns to Avoid

### General AI-Slop Patterns (from /social Phase 2 audit)

Kill on sight in any newsletter draft:
- "In today's fast-paced world..."
- "As we continue to navigate..."
- "I'm excited to share..."
- "Game-changer" / "paradigm shift" / "thought leader"
- "This is a friendly reminder"
- "Don't miss out on..."
- "Synergy" / "ecosystem" / "holistic" / "robust" / "scalable"
- "Move the needle" / "low-hanging fruit" / "best-in-class"
- "Value-add" / "actionable insights" / "seamless experience"
- Opening with "Happy [day of week]!"
- Ending with "Stay tuned for more exciting updates!"
- Three paragraphs in a row starting with "I"
- Rhetorical question as opener when you immediately answer it
- Ellipsis at end of section to "build suspense"
- Bolding random phrases for visual interest rather than emphasis
- More than one exclamation point per issue
- Ending with a motivational quote from someone famous

### Newsletter-Specific Anti-Patterns

- **Daily emails on a content newsletter:** Burns list goodwill. Daily works only for pure curation (Morning Brew model). Weekly or biweekly for ideas-based writing.
- **"We're excited to announce..." openers:** Positions you, not the reader. Rewrite around what the reader gets.
- **Multiple CTAs per issue:** One ask per issue maximum. Two CTAs = neither gets clicked.
- **Housekeeping sections:** "First, a quick announcement..." Nobody reads it. Fold it into the value or cut it.
- **"In case you missed it" recaps:** Signals you don't trust your own content. If it's worth reading, re-link with new context. Don't apologize for the archive.
- **Subject lines trying to be clever over specific:** "The one thing you need to know" loses to "What we learned from 6 months of LinkedIn experiments" every time.
- **Long preamble before the point:** If the first paragraph could be deleted and the issue would be better, delete it.
- **Generic social share ask:** "Please forward this to someone who would enjoy it" is spam. A specific forward ask ("If you manage a team of 5-15 people, forward this to one person you know who's dealing with this") converts.

---

## Metrics Dashboard Template

Track these. Ignore open rate (Apple MPP inflated it into meaninglessness after 2021).

### Engagement Metrics

| Metric | What It Measures | Target | Red Flag |
|--------|-----------------|--------|----------|
| Reply rate | Real human engagement | 0.5-1%+ on any size list | Below 0.2% = list doesn't trust you |
| CTOR (click-to-open rate) | Interest from people who opened | 10-20% for B2B content | Below 5% = mismatch between subject and body |
| Forward rate | Word-of-mouth signal | 0.3%+ | Hard to measure without dedicated tool |
| Unsubscribe rate | Relevance signal | Under 0.2% per issue | Spike after an issue = that topic or tone was wrong |

### Growth Metrics

| Metric | What It Measures | Target |
|--------|-----------------|--------|
| New subscribers per week | List velocity | Varies by stage; direction matters more than absolute |
| Referral rate | Organic amplification | Track with SparkLoop or Kit referral feature |
| Source attribution | Where readers come from | Tells you which list-building activity is working |

### Business Metrics (if newsletter is commercial)

| Metric | What It Measures |
|--------|-----------------|
| Subscriber-to-opportunity conversion | CRM-tracked: which subscribers become leads or customers |
| Revenue per subscriber | For paid newsletters or newsletters with a paid product |
| Churn rate (paid) | For paid tiers |

### What Not to Track

- Raw open rate (MPP inflated, meaningless for list health)
- Follower count on the distribution platform
- Impressions if re-posted to social
- "Engagement rate" as a catch-all

---

## Repurposing Framework

Every newsletter issue should spawn 3-5 social assets. Do this immediately after sending - the content is freshest and you have it in the draft.

### The 5-Asset Standard

1. **LinkedIn post:** Pull the best 1-2 sentences from the issue as a hook. Add 2-3 sentences of additional context not in the newsletter. End with a link to subscribe or to the full issue archive.

2. **X thread:** Take the core argument. Break it into 4-6 tweets. First tweet is the hook (strongest counter-intuitive claim or specific number). Last tweet drives to subscribe.

3. **Quote card (Instagram/LinkedIn image):** Extract the most self-contained, shareable sentence from the issue. One sentence only. No context needed. This is the "would screenshot and share" test.

4. **Data/stat standalone:** If the issue contains a number or finding, extract it. Post it with the one-sentence "what this means" as a standalone piece.

5. **Short video hook (optional):** If you're doing video - record 60 seconds expanding on the one counter-intuitive point from the issue. This is not a recap; it's an extension.

### Repurposing Rules

- Repurpose the DAY you send. Don't queue it for later; it won't happen.
- Social posts are NOT excerpts. They are remixes. Take the idea, not the sentences.
- Tag the issue in your repurpose-ideas.md so you have a record.
- Each repurposed post should drive back to the subscribe link, not just the issue URL.

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these five questions, one at a time. Wait for each answer before asking the next. Do NOT ask them all at once.

1. "What's your newsletter about? In one sentence, what do readers get from you that they can't get elsewhere?"

2. "Who is the ONE person you're writing to? Name them, describe them. What do they do, what keeps them up at night?"

3. "What's your cadence going to be - weekly, biweekly, or something else? And is there a reason for that choice?"

4. "Is this a brand new newsletter, an existing one you want to improve, or do you need to draft today's issue specifically?"

5. "What's the single most useful thing you've learned in the last 30 days that you haven't written about yet?"

After collecting all five answers, summarize what you heard and confirm before proceeding:

```
═══════════════════════════════════════════════════════════════
INTERVIEW COMPLETE
═══════════════════════════════════════════════════════════════

  Newsletter: [one-line description]
  Reader:     [named or described person]
  Cadence:    [weekly / biweekly / other]
  Mode:       [new / improve / draft issue]
  Seed idea:  [the thing they haven't written about yet]

  Does this capture it? Confirm to proceed.
═══════════════════════════════════════════════════════════════
```

Save answers to `.newsletter-reports/YYYYMMDD-HHMMSS-<slug>/interview-answers.md`.

---

## PHASE 1: CONTEXT LOAD

### 1.1 Check for Existing Brand Context

```bash
# Check for project-local brand context
ls .marketing-context/brand.json 2>/dev/null

# Check for global brand context
ls ~/.claude/marketing-global/brand-*.json 2>/dev/null
```

If found, load and apply to voice calibration. Brand voice from `/marketing` takes precedence.

### 1.2 Analyze Existing Newsletter (if improving or drafting issue)

If the user provides URLs to past issues, use WebFetch to retrieve up to 3. Analyze:

- **Opening pattern:** How does each issue begin? Scene, question, statement, number?
- **Voice fingerprint:** What sentences could only this person have written? What makes the voice recognizable?
- **Structure pattern:** How are issues organized? What sections appear consistently?
- **CTA pattern:** When and how does the call to action appear? Is it hard or soft?
- **Subject line pattern:** Specific vs. clever? Question vs. statement? Length?
- **What's working vs. what feels off:** Be honest about where the voice drops or the structure gets weak.

Save analysis to `research-notes.md`.

### 1.3 Setup Session Folder

```bash
SESSION_DIR=".newsletter-reports/$(date +%Y%m%d-%H%M%S)-$(echo '$SLUG' | tr ' ' '-' | tr '[:upper:]' '[:lower:]')"
mkdir -p "$SESSION_DIR"
grep -q ".newsletter-reports" .gitignore 2>/dev/null || echo ".newsletter-reports/" >> .gitignore
```

---

## PHASE 2: PLAN

Write `plan.md` in the session folder covering:

### 2.1 Newsletter Positioning

Answer these in one sentence each:
- What does this newsletter help readers DO (not just know)?
- Who is the real competition for inbox attention (other newsletters, not just subject matter)?
- What would readers lose if this newsletter stopped?

The positioning statement format:
```
For [named reader type], [Newsletter Name] is the [frequency] newsletter that [core benefit],
unlike [what readers get elsewhere], [Newsletter Name] [key differentiator].
```

### 2.2 Subject Line Strategy

Document the rule that governs every subject line:
- Specific or clever? (Default: specific wins. Clever only if your audience expects it.)
- Questions or statements?
- Length target (under 50 characters for mobile-first readers)
- What information goes in preview text vs. subject

### 2.3 Structure Template Selected

Choose one based on the reader's stated content and voice. Document the choice and why.

**Option A - Single-Idea Essay (Khe Hy / Morgan Housel pattern):**
Best for: founders with strong opinions, personal experience-based writing, B2B operators

**Option B - Practitioner Synthesis (Lenny Rachitsky pattern):**
Best for: aggregators, interviewers, people who talk to many practitioners

**Option C - Curation + Commentary (Ben Tossell pattern):**
Best for: high-frequency curators, AI/tech news, time-constrained writers

**Option D - Long-Form Narrative (Packy McCormick pattern):**
Best for: once-weekly deep dives, writers with dedicated reader time blocks

### 2.4 Voice Rules (Specific Linguistic Patterns)

Produce 5-7 concrete rules for THIS newsletter's voice. Not generic advice - specific rules:
- Example of good (could only be this person): [sentence]
- Example of bad (could be anyone): [sentence]
- Sentence length tendency: [short/varied/long]
- Personal disclosure: [high/medium/low - and what types are in bounds]
- Opinion directness: [always names the position / sometimes hedges / prefers implication]

### 2.5 First 3 Issue Topics

Not just titles - brief argument for each:
- Issue 1: [topic] - Core argument in one sentence. Why now?
- Issue 2: [topic] - Core argument in one sentence. Why this follows Issue 1?
- Issue 3: [topic] - Core argument in one sentence. Natural cadence?

### 2.6 List-Building Hook

Identify the one-line description of who should subscribe and what they get. This goes in bio links and social distribution:
- Not: "Subscribe to my newsletter about marketing"
- Yes: "Every Tuesday: one thing I learned about B2B content that week, plus what to do with it. 2 min read."

### 2.7 Metrics to Track

Confirm the tracking setup (see Metrics Dashboard Template above). Confirm reader is NOT tracking open rate as primary signal.

### 2.8 Hard Gate

Present the full plan. Do not proceed until user explicitly approves or requests revisions.

```
═══════════════════════════════════════════════════════════════
PLAN REVIEW
═══════════════════════════════════════════════════════════════

  Positioning:    [one-liner]
  Reader:         [named person or type]
  Structure:      [Option A/B/C/D + reason]
  Voice rule #1:  [most important rule]
  Issue 1 topic:  [topic + argument]
  List-build CTA: [one-line description]

  Approved? Or revise?
═══════════════════════════════════════════════════════════════
```

---

## PHASE 3: GENERATE

Apply the approved structure template. Mode determines what gets generated.

### Mode A: Drafting an Issue

Apply the structure template below. Fill every section. Do not leave placeholders.

```
[Subject line]
[Preview text: extends subject, does NOT repeat it]

---

[Opening: one sentence, specific situation/number/scene. NOT "Happy Tuesday!"]

[Body: 200-600 words. ONE idea fully developed. No subheadings required.
Every paragraph earns its place. Cut anything that doesn't move the argument forward.]

[So what: 2-3 sentences connecting the idea to the reader's specific situation.
Not "so you can see why this matters" - make it concrete.]

[Optional: ONE curated link or resource with specific 2-3 sentence commentary.
Why this, why now, what you actually think about it.]

[CTA if commercial: ONE ask. Soft. After value has been delivered.]

[Signature: name + one-line context. Short.]
```

**Exclusions - remove these before delivering draft:**
- Multiple CTAs
- Housekeeping sections ("Before we get into it...")
- Generic shares ("Check out this tweet from...")
- "In case you missed it" recaps
- Any sentence that could be deleted without losing anything

### Mode B: Building a Structure System

Produce reusable templates for all four newsletter types:

**Welcome Sequence (4-email flow):**
- Email 1 (immediate): Deliver the lead magnet or confirm subscription. One sentence of who you are. No pitch.
- Email 2 (day 3): "Here's what to expect." The 3 things you write about and why. One link to a best issue.
- Email 3 (day 7): A real issue. Full format. No meta-commentary about being a welcome email.
- Email 4 (day 14): Ask a question. "What are you working on right now?" This seeds replies and segments.

**Weekly Issue Template:**
Use the single-issue structure above. Applied consistently across every issue.

**Quarterly "State of X" Issue:**
Longer format (600-1200 words). Year-in-review or mid-year check-in structure:
- What you thought would happen vs. what did
- One belief that changed (with specific evidence)
- What's coming in the next quarter (concrete, not vague)
- One recommendation for readers based on what you learned

**Launch/Promo Issue (max 1 per month):**
Constraint: commercial pitch only after substantial value has been delivered in the issue. Structure:
- Open with a useful insight (not "I'm excited to announce")
- Deliver real value in the body (makes the CTA feel earned)
- CTA appears after value: one sentence, one link, one ask
- No urgency fabrication ("spots are limited")

### Voice Rules Applied During Generation

- Write to one named person, not an audience
- First draft is a message, not a publication
- Cut anything you wouldn't say to a smart colleague face-to-face
- No "as we continue to navigate"
- Kill all intensifiers (very, really, quite, extremely, incredibly)
- One opinion stated plainly is worth ten hedged observations

---

## WORKED EXAMPLE: B2B SaaS Founder Newsletter

**Context:** Marcus runs a project management SaaS for agencies (10-50 person teams). He writes weekly. His reader is "Jordan" - an agency owner hitting $1M ARR who is drowning in client status updates and team coordination.

**Interview answers:**
1. Newsletter: "Practical ops patterns for agency owners scaling past $1M without hiring a full-time ops person"
2. Reader: Jordan, 38, runs a 12-person digital agency, builds client dashboards in spreadsheets, spends 6 hours/week in status update calls
3. Cadence: Weekly, Tuesdays, because Jordan checks email before client calls at 9am CT
4. Mode: First issue, new newsletter
5. Seed idea: "We switched to async-first status updates 90 days ago and client call volume dropped 40%"

**Subject line:** "What happened when we banned sync status calls (90 days of data)"

**Preview text:** "40% fewer client calls, same client satisfaction score. Here's the setup."

**Issue body:**

---

Ninety days ago we told our 14 active clients we were switching to async status updates only.

No more weekly check-in calls. A shared doc updated every Tuesday morning. A 3-minute Loom every other week for anything that needed explanation.

Three clients pushed back. Two asked for a transition call to understand the system. One said they'd consider leaving.

Here's what happened: client call volume dropped 40%. Client satisfaction scores held at 4.6/5. The client who threatened to leave renewed at a higher rate.

The system is straightforward. Every client gets a status doc structured in three sections: what shipped last week, what's in progress this week, what's blocked and why. We update it by 8am Tuesday. Clients read it before the call that no longer exists.

The 40% drop in calls happened because 80% of what we were saying on calls was information the client already had. The call was mostly a ritual, not a decision. Async forced us to write it down clearly instead of explaining it verbally.

What didn't work: clients who want relationship time through the status call still needed something. We replaced calls with a 15-minute monthly check-in that is explicitly NOT about project status. Just: how's the business, what's coming up, what do you need from us that isn't in the work. Three of our longest clients use it for actual strategy conversation now.

If you run a 10-20 person agency and you're spending more than 4 hours a week in client status calls, this is probably worth testing. The template is simple enough to build in 30 minutes.

---

**So what:** If Jordan can cut 2-3 status calls per week, that's 2-3 hours back. The real gain is cognitive - async forces clearer writing, which forces clearer thinking about what's actually happening in the project.

**CTA (soft):** "Reply if you want the doc template. I'll send it."

**Signature:** Marcus / Founder, [Product] / We build PM tools for agency teams.

---

**Voice fingerprint sentence (only Marcus could write this):** "Three clients pushed back. Two asked for a transition call to understand the system. One said they'd consider leaving."

The specificity (14 clients, 90 days, 40%, 4.6/5 score) is the voice fingerprint. A generic version would say "some clients were skeptical." Marcus names exactly what happened.

---

## PHASE 4: VERIFICATION

Before delivering the draft, run this checklist:

- [ ] Re-read the plan. Does the issue match the positioning and voice rules?
- [ ] Voice fingerprint: identify the ONE sentence in this issue that only this person could write. If you can't find it, the draft needs revision.
- [ ] AI-slop audit: scan for the 30 patterns listed above. Flag and rewrite any match.
- [ ] CTA placement: does value precede the ask? If the CTA is in the first half of the issue, move it.
- [ ] Subject line test: is it specific or clever? If clever, is the reader the kind of person who expects clever? If not, rewrite toward specific.
- [ ] Opening line test: does the first sentence start mid-scene with a number, specific situation, or tension? If it starts with "I've been thinking about..." or "Happy Tuesday" - rewrite.
- [ ] One-idea test: underline every distinct argument in the body. If you underlined more than one, the issue is trying to do too much. Cut to one.

Save verification notes to `verification.md`.

---

## PHASE 5: SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

Use the unified template. Domain-specific additions for `/newsletter`:

```
═══════════════════════════════════════════════════════════════
SITREP — /newsletter
═══════════════════════════════════════════════════════════════
Project:  [newsletter name]  |  Branch: [git branch if applicable]
Mode:     [new / improve / draft issue / structure system]
Status:   [COMPLETE / PARTIAL / BLOCKED]
───────────────────────────────────────────────────────────────

NEWSLETTER DETAILS
  Issue type:       [welcome / weekly / quarterly / launch / structure-system]
  Subject line:     [final subject line chosen]
  Alternatives:     [2 subject lines considered but not selected]
  Word count:       [X words]
  Read time:        [X min]

VOICE
  Voice fingerprint: [the ONE sentence only this person could write]
  Voice rules applied: [count]
  AI-slop patterns caught: [count, or "0 found"]

REPURPOSING OPPORTUNITIES
  LinkedIn post:    [1-line description of the angle]
  X thread:        [1-line description of the core thread]
  Quote card:      [the sentence identified as most shareable]
  Data/stat post:  [the number or finding extracted, if applicable]

STRUCTURE
  Template used:    [A/B/C/D + name]
  CTA position:     [after value - yes/no]
  Single idea test: [passed / failed + note]

OUTPUTS
  Session folder:   .newsletter-reports/[folder]/
  Files produced:
    - interview-answers.md
    - research-notes.md
    - plan.md
    - issue-draft.md
    - repurpose-ideas.md
    - verification.md
    - sitrep.md

METRICS TO WATCH
  Reply rate target:  0.5-1%+
  CTOR target:        10-20%
  Tracking open rate: [yes - warn / no - good]

───────────────────────────────────────────────────────────────
NEXT STEPS
  1. [Clear immediate action - send the issue, set up welcome sequence, etc.]
  2. [Follow-up - repurpose to social, set up next issue topic]

SUGGESTED NEXT SKILLS
  Issue ready to send:     Run /social to atomize into posts
  Need landing page copy:  Run /copy for subscribe page
  Full campaign context:   Run /marketing for brand framework
───────────────────────────────────────────────────────────────
Report: .newsletter-reports/[folder]/sitrep.md
═══════════════════════════════════════════════════════════════
```

---

## SESSION FOLDER STRUCTURE

```
.newsletter-reports/YYYYMMDD-HHMMSS-<slug>/
  interview-answers.md      # Raw answers from Phase 0
  research-notes.md         # Voice analysis of existing issues
  plan.md                   # Newsletter positioning, voice rules, topic plan (live-updated)
  issue-draft.md            # The drafted issue, ready to send
  repurpose-ideas.md        # 3-5 social assets this issue spawns
  verification.md           # Phase 4 checklist results
  sitrep.md                 # Final SITREP
```

All folders are gitignored via `.newsletter-reports/` in `.gitignore`.

---

## RELATED SKILLS

**Feeds from:**
- `/marketing` - brand context (ICP, positioning, voice) loads automatically from `.marketing-context/brand.json` or `~/.claude/marketing-global/brand-*.json`
- `/copy` - voice rules and prohibited phrases inform newsletter voice calibration

**Feeds into:**
- `/social` - every completed issue spawns 3-5 social assets; run immediately after send

**Pairs with:**
- `/copy` - for writing the subscribe landing page, welcome sequence, or lead magnet
- `/social` - run after each issue to atomize the content into platform-native posts

**Auto-suggest after completion:**
When a draft is complete, suggest: `/social` to build the repurposing assets from this issue.

---

## REMEMBER

- **One idea per issue.** If you can't summarize the issue in one sentence, it's two issues.
- **Specific beats clever** in subject lines, every time, for every audience except pure entertainment.
- **Name the reader.** Write to Jordan, not "agency owners." Jordan notices the difference.
- **Value before ask.** The CTA is permission, not a right. Earn it in every issue.
- **Reply rate is the signal.** If nobody replies, the issue didn't land. Open rate is noise.
- **Repurpose the same day.** The content is freshest when you just wrote it.
- **Voice fingerprint is non-negotiable.** Every issue must contain one sentence only this person could write.

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — Newsletter-first operator pipeline, 2026 patterns -->
<!-- Part of the Claude Code Skills Collection -->
<!-- Powered by Claude models: Haiku (mechanical extraction), Sonnet (draft generation), Opus (voice analysis) -->
<!-- License: MIT -->
