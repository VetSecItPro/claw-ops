---
description: "Storytelling craft skill - scene-setting, aha-moments, dialogue, opening patterns, voice fingerprinting. Called standalone or as sub-skill from /copy and /social for any asset where storytelling is the primary mechanism."
allowed-tools: Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Read, Write, Edit, Glob, Grep, Task
---

# /narrative - Storytelling Craft Pipeline

**Steel Principle: A story without a specific time, place, person, and action is not a story. It is an observation dressed in story clothes. Observations convert nobody.**

Produces story-driven content for any asset where narrative is the primary mechanism: customer stories, founder origin narratives, belief-flip posts, aha-moment arcs, dialogue-led trust pieces, and long-form essays. Works as a standalone skill or as a sub-skill called from `/copy` and `/social`.

> **When to use /narrative:**
> - The asset lives or dies on whether the story lands
> - You have a real moment, a real person, a real before/after
> - The insight needs to arrive mid-scene, not in the headline
> - You want the reader to figure something out rather than be told it
>
> **When NOT to use /narrative:**
> - You need pure conversion copy with CTAs - use `/copy`
> - You need social posts that are primarily tactical tips - use `/social`
> - The "story" is a hypothetical or composite - flag this, hypotheticals have lower trust than real scenes

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** Every asset must have a specific reader in mind before Phase 2; "audience" alone is too vague.
- **Steel Principle #2:** HARD GATE at Phase 2 - no prose is generated until the storytelling plan is user-approved.
- **Steel Principle #3:** Voice fingerprint is applied from saved samples, not improvised. If no voice samples exist, pause and collect.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "I know the voice, skip the fingerprint load" | Unreferenced voice produces generic-sounding copy within 2 paragraphs | Load the voice samples every time; voice drifts without anchoring |
| "The opening is fine, move on" | Openings that are "fine" get skipped; openings that earn the scroll get read | Write 3 opening options minimum; pick the one that earns attention |
| "The reader is obvious from context" | Generic readers produce generic prose; specific readers produce specific prose | Name the reader in one sentence before writing |
| "Skip the aha-moment, they will get it" | Stories without an aha land as summaries, not narratives | Every narrative needs one moment where the reader's understanding shifts |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

---

## RELATED SKILLS

**Feeds from:**
- `/brainstorm` - the approved narrative angle from brainstorm is the starting point when voice-driven work is needed
- `/marketing` - messaging framework provides the Tier 1 message that a narrative hooks to

**Feeds into:**
- `/copy` - storytelling output becomes the narrative scaffolding inside landing pages, emails, or sales pages
- `/social` - a story can be distilled into platform-native posts
- `/blog` - a fully-developed narrative becomes the long-form article
- `/newsletter` - anchor stories make the best newsletter openings

**Pairs with:**
- `/copy` - storytelling is invoked from /copy when the asset needs a narrative spine
- `/social` - called when a post is a story, not an announcement

**Auto-suggest after completion:**
When the narrative is approved, suggest: `/copy` to embed it in a conversion asset, or `/blog` to expand it into a full article.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Reports and artifacts live in gitignored `.narrative-reports/` directories
- Any temporary files or sessions are closed at the end of the run
- No persistent resources are left behind unless explicitly requested

---

## Phase 0: Socratic Interview (5 questions)

Ask these five questions before writing a single word. Do not collapse them into a form. Ask one, wait for the answer, then ask the next.

1. "What's the thing you want to convey? Not the lesson - the actual story or moment."
2. "Whose story is this? You, a customer, a team member, a hypothetical?"
3. "Where and when did this happen? Specific time, place, person?"
4. "What was the tension? What almost didn't happen, or went wrong before it went right?"
5. "What do you NOT want to tell the reader directly? What should they figure out themselves?"

**If the user can't answer question 3:** The story isn't ready. The details exist somewhere - help them find it. Ask: "Was this a call, an email, a Slack message, a meeting? What time of day? Where were you?"

**If the user can't answer question 5:** The story has no implicit layer. Everything is stated, nothing discovered. Ask: "What would you want someone to walk away thinking, without you having said it out loud?"

---

## Phase 1: Context Load

**Brand voice check:**
```bash
ls .marketing-context/ 2>/dev/null
cat .marketing-context/brand.json 2>/dev/null
```

If `brand.json` exists: load voice rules, prohibited words, and tone constraints before writing.

If not found: proceed with interview answers as the only voice source. Note in plan.md that no brand context was loaded.

**No research phase.** This is craft, not research. The raw material is the interview answers. Additional research produces generic context instead of specific scenes.

---

## Phase 2: Plan + HARD GATE

Create the session folder and write plan.md before generating anything:

```bash
mkdir -p .narrative-reports
grep -q ".narrative-reports" .gitignore 2>/dev/null || echo ".narrative-reports/" >> .gitignore
SLUG=$(echo "$ARGUMENTS" | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | cut -c1-40)
SESSION_DIR=".narrative-reports/$(date +%Y%m%d-%H%M%S)-${SLUG:-draft}"
mkdir -p "$SESSION_DIR"
```

Write `$SESSION_DIR/plan.md` with:

```markdown
## Narrative Plan

**Narrative structure:** [scene-setting / aha-moment arc / dialogue-led / belief-flip]
**Opening line pattern:** [pattern 1-5 from the 5 opening patterns below]
**Scene elements:**
  - Time: [specific - day, year, hour if known]
  - Place: [specific location]
  - Person: [one named or role-specific person - NOT "companies" or "teams"]
  - Action: [what they were doing when the story starts]
**Tension point:** [what almost didn't happen or went wrong]
**Implicit meaning:** [what the reader figures out themselves]
**Explicit meaning:** [what, if anything, is stated directly - should be minimal]
**Voice fingerprint target:** [draft of the one sentence only this person/brand could write]
**Asset destination:** [where this narrative will live - email, post, article, sales page]
```

**HARD GATE:** Present the plan. Ask: "Approved? Or revise?"

Do not write a single sentence of narrative until the plan is approved.

---

## Phase 3: Generate

### Scene-Setting Rules

Every scene must contain all four elements. Missing any one element makes it an observation, not a scene.

| Element | Required | Example |
|---------|---------|---------|
| Specific time | Day/year/hour | "2:17am on a Thursday" not "late at night" |
| Specific place | Named or described location | "his kitchen table in Phoenix" not "at home" |
| One person | Named or role-specific individual | "Marcus, their VP of Sales" not "the team" |
| Action in progress | What they were doing | "checked his Stripe dashboard" not "was working" |

**BAD:** "Companies often struggle with cash flow, especially in Q4 when receivables slow down."

**GOOD:** "It was 2:17am on a Thursday when Marcus checked his Stripe dashboard and realized the $80k he was counting on hadn't cleared."

The BAD version is an observation. It could appear in any article about any company. The GOOD version is a scene. Remove the names and you still know exactly what moment this is.

**Scene anti-patterns:**
- "Recently, a client of ours..." (when did this happen?)
- "One of our customers told us..." (which one? where were they?)
- "The team was struggling with..." (which person on the team? what were they doing when they noticed?)
- Background description instead of action ("She was in charge of marketing for a mid-size SaaS company" - this is biography, not scene)

---

### Aha Moment Arc (5 Steps)

**The mistake most writers make:** State the insight in the first paragraph. Then prove it. This destroys all tension - the reader already knows what they're supposed to think.

**The correct order:** The insight arrives at step 3, mid-scene. The reader discovers it alongside the character.

**Step 1 - Before state (specific, not abstract)**
Not: "Before they used our product, the company had manual processes."
Yes: "Every Monday morning, Sarah spent three hours in Excel reconciling expense reports that had already been submitted, reviewed, and approved in three separate tools."

**Step 2 - Tension (moment something didn't add up)**
The trigger. A specific moment when the before state became intolerable or incomprehensible.
Not: "The process was inefficient."
Yes: "On a Monday in March, she submitted the same invoice for the fourth time and got a rejection for a reason she'd already fixed twice."

**Step 3 - Insight arrives mid-scene (NOT as a list item)**
The insight is discovered inside the action, not announced after it.
Not: "This is when she realized the problem wasn't the tools, it was the workflow."
Yes: "She closed the laptop. Didn't reopen it. Called the vendor and asked not how to fix the submission, but why the process required four submissions at all."

**Step 4 - What changed (behavior, not belief)**
Show a changed action, not a changed opinion.
Not: "She now believes in streamlined workflows."
Yes: "She rebuilt the process from the approval step backward. The monthly reconciliation dropped from three hours to twenty minutes."

**Step 5 - Broaden to reader (1-2 sentences MAXIMUM)**
One small bridge. Do not lecture.
Not: "This teaches us that we should always question our existing processes and look for inefficiencies in our systems before buying new tools."
Yes: "Most process problems aren't tool problems. They're design problems that tools can't fix."

**BAD full arc (everything stated, nothing discovered):**
> Manual expense reporting wastes time. Sarah was spending 3 hours every Monday on it. After she found a better tool, the process took 20 minutes. If you're doing this manually, you're wasting time too.

**GOOD full arc (insight arrives mid-scene):**
> Every Monday morning, Sarah spent three hours in Excel reconciling expense reports that three other tools had already processed. In March, she submitted the same invoice for the fourth time, got a rejection, and instead of fixing the submission, she called the vendor and asked why four submissions were required at all. She rebuilt the process from the approval step backward. Mondays freed up. Not because she found a better tool. Because she stopped trying to fix the submission and started questioning whether the process made sense.

---

### Dialogue as Trust Tool

One sentence of actual speech builds more trust than three paragraphs of reported outcome. Dialogue is specific. It happened. The reader knows it wasn't fabricated.

**Rule:** Use direct quotes when you have them. Use reported speech only when the exact words weren't captured.

**Weak (reported assertion):**
"Our VP of Sales found the forecasting feature significantly more reliable than our previous solution."

**Strong (direct quote):**
"Our VP of Sales looked at the dashboard and said: 'This is the first time I've actually trusted a forecast.'"

The weak version could have been written by a marketing intern who never spoke to the VP. The strong version requires that the conversation happened.

**When to use dialogue:**
- When a customer said something that captures the before/after more precisely than description
- When the insight came from a conversation, not a chart
- When the speaker's word choice reveals something their summary wouldn't

**When NOT to fabricate dialogue:**
Never. Placeholder it: `[PLACEHOLDER: exact quote from Sarah about Monday process]`

**Dialogue formatting rules:**
- Use a colon before the quote, not a comma, for emphasis: `She said: "I've never trusted a forecast before."`
- Short quote in the middle of a sentence uses a comma: `She said "this is the first time" and meant it.`
- Partial quotes preserve the speaker's words; reported speech is yours

---

### 5 Opening Line Patterns

Use exactly one pattern per narrative. Match the pattern to the story's natural tension.

**Pattern 1 - Specific number, unexpected context**
The number creates specificity. The context creates surprise. The combination creates forward pull.
> "We sent 47 cold emails. One replied. That one closed at $180k."

Use when: The story has a counterintuitive ratio or result.

**BAD version:** "Cold email can work if you target the right people."
**GOOD version:** "We sent 47 cold emails. One replied. That one closed at $180k."

---

**Pattern 2 - Belief that turned out wrong**
State what you believed. Do not yet say how you were wrong. Let the narrative prove it.
> "I spent three years convinced more features would fix churn. It didn't."

Use when: The story is a lesson from failure, or a pivot in thinking.

**BAD version:** "Most founders think more features solve retention problems, but the real issue is onboarding."
**GOOD version:** "I spent three years convinced more features would fix churn. It didn't."

---

**Pattern 3 - Scene with tension**
Drop the reader directly into a moment that has something at stake.
> "The customer didn't say the product was bad. He said he'd 'figure it out himself.'"

Use when: The story's core tension is a relationship, a conversation, or a decision point.

**BAD version:** "Customer feedback can be misleading if you don't read between the lines."
**GOOD version:** "The customer didn't say the product was bad. He said he'd 'figure it out himself.'"

---

**Pattern 4 - Thing no one says out loud**
State something true that most people in the audience think but won't publish.
> "Most marketing advice is written by people who have never run a marketing budget."

Use when: The story challenges a consensus belief the audience privately doubts.

**BAD version:** "Marketing advice online isn't always practical for real budgets."
**GOOD version:** "Most marketing advice is written by people who have never run a marketing budget."

---

**Pattern 5 - Specific contradiction**
Two facts that shouldn't both be true. The gap between them is the story.
> "Our highest-converting landing page has three sentences and no images. Marketing team hated it."

Use when: The result defies the conventional wisdom the audience holds.

**BAD version:** "Sometimes simpler is better in landing page design, even when it goes against best practices."
**GOOD version:** "Our highest-converting landing page has three sentences and no images. Marketing team hated it."

---

### Belief-Flip Structure

A four-beat structure for any narrative built around a changed position.

**Beat 1 - What you used to believe**
State the old belief specifically. Not "I didn't fully understand X." The actual belief.
> "I believed the best way to scale a service business was to systematize everything, turn it into a process, and remove yourself from delivery."

**Beat 2 - The event or data that changed it**
A specific thing that happened. Not a gradual realization.
> "In 2023, we systematized our onboarding, removed founder involvement, and watched NPS drop 40 points in eight weeks."

**Beat 3 - What you believe now**
The replacement belief. Specific. Falsifiable. Someone could disagree with it.
> "The thing clients are buying in a premium service is access to a specific person's judgment. Systems are how you scale delivery, not how you replace the person."

**Beat 4 - What this means for the reader (1-2 sentences)**
Not a sermon. A pointer.
> "If your service margins are compressing and clients are churning after systematization, that's the signal. Not that your system is wrong - that what they bought isn't in the system."

**Full worked example:**

BAD (lesson stated first, narrative as illustration):
> "Many founders make the mistake of over-systematizing their service businesses. This is a problem because clients want personal attention. Here's a story that illustrates this issue..."

GOOD (belief-flip structure, lesson emerges from story):
> "For three years I believed the path to scale was systematization: document every step, train anyone to deliver it, remove the founder from the process. In 2023, we did exactly that with our onboarding. Eight weeks later, NPS was down 40 points and two clients had churned. What they were paying for wasn't the process. It was the judgment behind the process. Systems scale delivery. They don't substitute for the person clients hired."

---

### Voice Development Checklist

Run every narrative through these 8 checks before delivery. Fix any failure.

**1. Read out loud test**
Read the piece out loud, slowly. Any sentence you stumble on, wouldn't say to a respected colleague, or sounds like a press release gets rewritten. Non-negotiable.

Failing example: "This paradigm shift represents a fundamental reconfiguration of how value is delivered."
Fixed: "Everything we thought we knew about delivery was wrong."

**2. Opinion must be falsifiable**
Every opinion in the narrative must name someone who would disagree. If nobody could disagree, it's not an opinion - it's an observation without stakes.

Failing: "Building relationships with customers is important."
Fixed: "Most B2B founders prioritize acquisition over retention until they see the cohort data. I think that's backwards."

**3. One sentence only this person could write**
Every narrative must contain exactly one sentence that, if you removed the brand name, you still couldn't mistake for anyone else. This is the voice fingerprint. Identify it. If it doesn't exist, write it.

Generic: "We learned a lot from that experience."
Specific: "The invoice rejection was the best thing that happened to our Q2 - it told us where the real problem was before we spent another quarter solving the wrong one."

**4. Name the exception where advice fails**
Every insight in the narrative has conditions under which it doesn't apply. Name one. This earns trust by demonstrating that the claim has been pressure-tested.

Without exception: "Simpler landing pages convert better."
With exception: "Simpler landing pages convert better for low-consideration purchases. If someone is signing a $50k contract, they need the full argument."

**5. Match sentence length to emotional weight**
Short sentences for impact. Long sentences for context, nuance, complexity. Never uniform rhythm - uniform rhythm is the clearest signal of AI-generated text.

Uniform (AI pattern): "We made the change. It worked out. The team was happy. Results improved. We learned a lot."
Varied: "We made the change. Three weeks later, results were up 40%. The team had been skeptical - they'd seen too many 'process improvements' that created work without removing it - but even they agreed this one was different."

**6. Use your actual vocabulary**
If you wouldn't use a word in a conversation with your best client, cut it. "Leverage," "utilize," "optimize," "facilitate" are all words nobody actually says. Write what you'd say.

Not-your-vocabulary: "We sought to leverage the insights gleaned from the aforementioned data."
Your vocabulary: "We took what the data was telling us and actually changed how we did the work."

**7. Kill all intensifiers**
Delete: very, really, quite, extremely, incredibly, highly, deeply, truly, absolutely, certainly.
Every intensifier is a marker that the underlying statement doesn't stand on its own. Either make the statement stronger or cut the intensifier.

With intensifier: "The results were extremely impressive and really changed how we thought about the problem."
Without: "The results changed how we thought about the problem."

**8. No compliment sandwich**
Start with the point. Not with what you appreciate about the reader, not with a disclaimer, not with a warm-up. The point, in sentence one.

Compliment sandwich: "This is something a lot of founders struggle with, and there's no shame in that. What I've found is that the answer isn't always obvious. But if you look carefully..."
Direct: "The answer is in the churn data. Most founders don't look there first."

---

## Phase 4: Verification

After generating the narrative, run all of the following checks before delivering:

**Scene specificity check**
- [ ] Time: is a specific time present? (not "recently" or "last year")
- [ ] Place: is a specific place named or described?
- [ ] Person: is ONE person present (not "the team" or "companies")?
- [ ] Action: is the person doing something, not just being described?

**Structure check**
- If aha-moment arc: was the insight withheld until step 3?
- If belief-flip: are all four beats present?
- If dialogue-led: is there at least one direct quote or a clear placeholder?

**Opening pattern check**
- Is the opening line from one of the 5 patterns?
- Test: could this opening have been written by a different brand? If yes, rewrite.

**Voice fingerprint**
- Identify the one sentence only this person/brand could write
- Write it out explicitly in verification.md
- If it doesn't exist in the draft, write it and insert it

**AI-slop audit (30 patterns)**
Check for any of these in the narrative. Rewrite every hit.

| # | Pattern | Example to avoid |
|---|---------|-----------------|
| 1 | "In today's fast-paced world..." | Any variation of this opener |
| 2 | "Let's dive in" | Any use of "dive in" or "let's explore" |
| 3 | "Unpack this" | "Let me unpack what happened" |
| 4 | "It's more important than ever" | "Trust is more important than ever in 2026" |
| 5 | "Game-changer" | "This was a game-changer for our team" |
| 6 | "Cutting-edge" | "Using cutting-edge technology" |
| 7 | "Landscape" as a noun | "The marketing landscape is shifting" |
| 8 | "Navigating [noun]" headlines | "Navigating Uncertainty in 2026" |
| 9 | "At the end of the day..." | Any use of this phrase |
| 10 | "It's not just about X, it's about Y" | "It's not just about features, it's about outcomes" |
| 11 | Three-word subheadings ending in "Matters" | "Trust Matters Most" |
| 12 | Numbered lists of exactly 5 or 10 items | When the number is artificial |
| 13 | "The key takeaway here is..." | Any variation |
| 14 | Opening with unasked rhetorical questions | "Have you ever wondered why...?" |
| 15 | "In conclusion" / "To summarize" | Any closing recap phrase |
| 16 | Uniform sentence-length rhythm | All sentences same length |
| 17 | "Harness the power of..." | "Harness the power of storytelling" |
| 18 | "Leverage [noun] to [verb]" | "Leverage data to drive decisions" |
| 19 | "Empower your team to..." | Any use of "empower" as a verb |
| 20 | "Unlock [benefit]" as standalone hook | "Unlock your potential" |
| 21 | Symmetrical paragraph structure | Every paragraph same structure |
| 22 | "Furthermore," "Moreover," "Additionally" | Connector words that sound academic |
| 23 | "Thought leadership" as self-description | "This piece of thought leadership..." |
| 24 | Emoji structural signposts | "X Done Y Done Z Done" |
| 25 | Hook where swapping one word makes any other post | Generic hooks |
| 26 | "The truth is..." before an obvious statement | "The truth is, relationships matter" |
| 27 | Closing "What would you add?" without earning it | Unearned engagement baiting |
| 28 | Fable-structure stories with no mess or ambiguity | Too-clean story arcs |
| 29 | Universally-true advice | "Communicate clearly with your team" |
| 30 | Stats with no source/year/methodology | "Studies show that 73% of..." |

**"Read out loud" final test**
Read the full narrative out loud. Any sentence you wouldn't say - flag and rewrite.

Save verification results to `$SESSION_DIR/verification.md`.

---

## Phase 5: SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

Write to `$SESSION_DIR/sitrep.md` AND display on screen:

```
================================================================
SITREP - /narrative
================================================================
Project:   [project or brand name]
Asset:     [email / post / article section / landing page story]
Started:   [YYYY-MM-DD HH:MM]
Completed: [YYYY-MM-DD HH:MM]
Status:    [COMPLETE / PARTIAL / BLOCKED]
----------------------------------------------------------------

NARRATIVE
  Structure:        [scene-setting / aha-moment arc / dialogue-led / belief-flip]
  Opening pattern:  [Pattern 1-5 - name it]
  Scene elements:
    Time:           [what was used]
    Place:          [what was used]
    Person:         [who]
    Action:         [what they were doing]

VERIFICATION
  Scene specificity:    [PASS / FAIL - details]
  Insight withheld:     [PASS / FAIL - arrives at step 3?]
  Voice fingerprint:    [the identified sentence]
  AI-slop patterns:     [count caught] caught, [count rewritten] rewritten

FINDINGS
  CRITICAL: 0  |  HIGH: 0  |  MEDIUM: [n]  |  LOW: [n]

OUTPUT
  Narrative draft:  [path to narrative-draft.md]
  Session folder:   [full path]

NEXT
  Ready to use in asset: /copy or /social
  If needs revision:     revise plan.md and regenerate
================================================================
```

---

## Session Folder Structure

```
.narrative-reports/YYYYMMDD-HHMMSS-<slug>/
  interview-answers.md     <- raw answers from Phase 0 questions
  plan.md                  <- live-updated through the session
  narrative-draft.md       <- the generated narrative
  verification.md          <- all Phase 4 checks, results, fingerprint
  sitrep.md                <- SITREP written here + displayed on screen
```

All session folders are gitignored via `.narrative-reports/` in `.gitignore`.

---

## 16 Buzzwords to Avoid (2026)

Remove any of these from every narrative, without exception:

`ecosystem` / `synergy` / `value-add` / `thought leadership` / `robust` / `scalable` / `holistic` / `drive [noun]` / `actionable insights` / `best-in-class` / `seamless` / `move the needle` / `low-hanging fruit` / `boil the ocean` / `circle back` / `take it offline`

If the meaning requires one of these words, rewrite the sentence to use specific language instead.

**BAD:** "This seamless integration delivers actionable insights to drive revenue."
**GOOD:** "You connect the tool in ten minutes. The data shows which deals are stalling and why."

---

## 5 Voice Markers That Signal Human

Actively build these into narratives where they fit the story. They are not tricks - they are accurate signals that a human, not a system, wrote this.

**1. Sentence fragments used intentionally**
Not a mistake. A rhythm choice.
> "She rebuilt the process from scratch. Took three weeks. Nobody complained."

**2. Acknowledge counterargument without resolving it**
AI resolves everything. Humans sometimes don't know.
> "I don't fully know why this version worked and the previous one didn't."

**3. Self-interruption**
The thought that corrects itself mid-sentence.
> "We thought the problem was pricing - actually, that's not right. Pricing was a symptom. The problem was positioning."

**4. Cite someone you disagree with, fairly**
Before disagreeing, accurately represent the other view.
> "The standard advice is to nail one channel before adding another. I've seen this work. I've also seen founders who found their second channel first and used it to validate whether the first was worth doubling down on."

**5. Admit sample size**
Name the limits of your evidence before making the claim.
> "This worked for us. One company, one team, one moment in 2024. I'd be careful about treating it as a rule."

---

## Related Skills

**Feeds from:**
- `/marketing` - load voice rules and ICP before running any narrative for a brand

**Feeds into:**
- `/copy` - storytelling in landing page hero sections, email openers, sales page origin narratives
- `/social` - story-driven posts where the narrative is the mechanism, not the hook
- `/blog` - long-form narrative structure, especially for origin stories and belief-flip essays
- `/newsletter` - issue structure built around a single narrative arc

**Pairs with:**
- `/copy` - narrative for the emotional layer, copy for the conversion layer
- `/social` - narrative generates the story, social formats it for the platform

**Called as sub-skill from:**
- `/copy` Phase 2 when asset type is story-led (customer story, founder origin, belief-flip email)
- `/social` Phase 1 when post type is "honest update," "teardown," or "belief-flip"

---

## Rules

- No em dashes anywhere in generated narratives
- No sparkle icons
- Max 1000 lines (this file)
- Every narrative requires a real story - no hypotheticals presented as real
- HARD GATE after plan.md - nothing written until plan is approved
- Session folder always created and gitignored
- SITREP always last

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill - Storytelling craft for any content type -->
<!-- Part of the Steel Motion LLC Skills Collection -->
<!-- Powered by Claude Sonnet (craft + reasoning) -->
