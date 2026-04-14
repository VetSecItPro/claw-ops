---
description: "/social — Social media content pipeline for any platform and product: write posts, build content calendars, adapt tone per channel, schedule-ready output. LinkedIn, X, Instagram, TikTok, Threads."
allowed-tools: Bash(git *), Bash(mkdir *), Bash(ls *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, Skill, WebFetch, WebSearch
---

# /social — Social Media Content Pipeline

**Steel Principle: Every post must serve a purpose (educate, entertain, convert, or build trust) and be adapted to the platform's native format. Generic reposts of the same caption to every platform are prohibited.**

Creates platform-native social media content for any product, service, brand, or creator. Give it context about what you're promoting and which platforms you're on; it returns ready-to-publish posts adapted to each platform's format, tone, and audience behavior.

**FIRE AND FORGET** — Tell it what to post about, which platforms, and any constraints. It handles research, drafting, variants, and calendar output.

> **When to use /social:**
> - Writing individual posts or a batch of posts for any platform
> - Building a content calendar (daily/weekly/monthly)
> - Adapting existing content (blog post, announcement, case study) into platform-native posts
> - Generating post variants to test different angles
> - Building a repeatable posting cadence for a product or brand
>
> **When NOT to use /social:**
> - You need a full go-to-market strategy - use `/marketing` instead
> - You need long-form copy (landing page, email) - use `/copy` instead
> - You need to actually publish/schedule posts via API - this skill writes content, not deploys it

---

## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** Every post is adapted to platform norms - length, format, hashtag usage, CTA style all differ by platform
- **Steel Principle #2:** No calendar is produced without a defined content mix - pure promotional content without educational/entertaining content will kill reach
- **Steel Principle #3:** Hooks are non-negotiable - the first line of every post must earn the scroll-stop; weak hooks get revised before delivery

### Social-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "Just post the same thing everywhere, people don't overlap" | Platform audiences have different expectations; LinkedIn prose bombs on TikTok, TikTok hooks sound cringe on LinkedIn | Adapt format AND tone for each platform from the same source idea |
| "The hook doesn't matter that much, the content is good" | Without a scroll-stopping first line, nobody reads the good content; the hook IS the product on social | Rewrite until the first line earns the click/expand without clickbait |
| "Pure product posts are fine, we need to drive conversions" | Purely promotional feeds lose followers and algorithmic reach; value must come first | Apply the 4:1 rule: 4 value posts for every 1 promotional post |
| "Hashtags should be everywhere and numerous" | Platform behavior varies: 3-5 targeted hashtags on LinkedIn, none on X (hurts reach), 3-10 on Instagram | Apply platform-correct hashtag strategy per post |
| "The calendar doesn't need variety, we can repeat formats" | Repeated format fatigue causes unfollows and suppresses reach | Plan a content mix: minimum 3 distinct post formats per week |
| "Emojis make everything more engaging" | On LinkedIn and X, overuse reads as unprofessional; match the brand voice | Use emojis only where they fit the brand tone and platform norm |

---

## STATUS UPDATES

> Reference: [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)

```
📱 /social Started
   Product/Brand: [name]
   Platforms:     [list]
   Mode:          [single post / batch / calendar]

🔍 Phase 1: Context Gathering
   ├─ Brand voice captured
   ├─ Platform profiles noted
   └─ Source content identified

✍️  Phase 2: Content Creation
   ├─ Posts drafted: [count]
   ├─ Platforms covered: [list]
   └─ Variants generated: [count]

📅 Phase 3: Calendar Output (if requested)
   ├─ Days covered: [count]
   ├─ Posts scheduled: [count]
   └─ ✅ Calendar ready

✅ Done
   Output saved to: .social-reports/[slug]/
```

---

## OUTPUT STRUCTURE

```
.social-reports/
├── [campaign-slug]/
│   ├── CALENDAR.md             # Full content calendar (if requested)
│   ├── posts/
│   │   ├── linkedin.md         # All LinkedIn posts
│   │   ├── x.md                # All X (Twitter) posts
│   │   ├── instagram.md        # All Instagram posts
│   │   ├── tiktok.md           # All TikTok scripts/captions
│   │   └── threads.md          # All Threads posts
│   └── content-ideas.md        # Backlog of unused ideas
```

---

## PHASE 0: INTAKE

Parse the user's request for:

- **Product/Brand:** What is being talked about? What does it do? Who is it for?
- **Platforms:** Which platforms? (LinkedIn, X, Instagram, TikTok, Threads, Facebook, YouTube Shorts)
- **Mode:** Single post, batch of posts, or full content calendar?
- **Source material:** Blog post to repurpose? Announcement? General topic? Product feature?
- **Tone/Voice:** Professional, casual, technical, playful, authoritative?
- **Goal:** Drive traffic, build audience, generate leads, build trust, announce something?
- **Constraints:** Character limits already known, specific CTAs required, links allowed/not, hashtag preferences

If platform is not specified, ask. Platform-specific rules are mandatory and cannot be defaulted.

**Setup:**
```bash
mkdir -p .social-reports
grep -q ".social-reports" .gitignore 2>/dev/null || echo ".social-reports/" >> .gitignore
```

**Brand context lookup (before asking the user anything):**
1. Check `.marketing-reports/brand.json` in the current project - if found, load it as brand context
2. If not found, check `~/.claude/marketing-global/` for named brand files: `ls ~/.claude/marketing-global/ 2>/dev/null`
3. If global brands exist, present options: "Found global brands: [list]. Use one for brand context, or describe the brand fresh?"
4. If user selects a global brand, load `~/.claude/marketing-global/brand-[name].json` as brand context
5. At end of intake, if new brand context was built from scratch, offer: "Save this brand context for reuse? [global / project-local / skip]"

---

## PHASE 1: CONTEXT & BRAND VOICE

### 1.1 Understand the Brand Voice

If the user provides a URL (company site, existing social profile), use WebFetch to extract:
- Existing tone and vocabulary choices
- Topics they post about
- Engagement patterns (what gets responses)
- What they avoid

If no URL or prior context, ask for 2-3 example posts that represent the voice they want.

**Brand voice spectrum - identify where this brand sits:**

```
Formal ←————————————————————————→ Casual
Expert ←————————————————————————→ Accessible
Serious ←————————————————————————→ Playful
Corporate ←————————————————————————→ Human/Personal
```

Document: [axis 1 position], [axis 2 position], [axis 3 position], [axis 4 position]

Example: "Casual, Expert, Serious-leaning, Human" = a technical founder who explains things clearly without stuffiness.

### 1.2 Platform-Specific Rules

Apply these rules to every post for each platform:

**LinkedIn:**
- Optimal length: 150-300 words for thought leadership; 50-100 for short-form
- Hook: First line must work as a standalone sentence without "more"
- Format: Short paragraphs (1-2 sentences max), white space is engagement
- Hashtags: 3-5 targeted, placed at end
- No links in body (they suppress reach) - use first comment
- Personal voice outperforms company page voice
- CTA style: End with a question or direct ask

**X (Twitter):**
- Single tweet: 240-280 characters (leave room for replies)
- Thread: First tweet is the hook + "Thread:" or number indicator, each subsequent tweet is self-contained
- No hashtags in body (hurts reach); 1 max if truly relevant
- Links: Fine in tweets, suppress on threads (put link at end)
- Tone: Punchier, more opinionated than LinkedIn
- CTA: Retweet/share asks, follow asks, or direct to link

**Instagram:**
- Caption length: 125-150 chars visible before "more"; rest can be longer
- Hook must work in first 125 chars
- Hashtags: 3-10 in caption or first comment; mix of niche + broad
- CTA: "Link in bio" is the standard
- Heavy emoji use accepted (if brand voice supports it)
- Story content: short, high-frequency, ephemeral

**TikTok:**
- Caption: 150 chars max (it's secondary to video)
- Content is video-first; if writing TikTok, write a script/scene description
- Hook is first 2-3 seconds of the video script
- Trending audio/format awareness matters
- Hashtags: 3-5 including 1-2 trending

**Threads:**
- Optimal length: 200-500 chars
- Conversational, low-pressure format
- No hashtags (not indexed)
- Links are fine
- Engages as a community conversation starter

**Facebook:**
- Optimal length: 40-80 words for organic; longer for groups
- Links in post are fine
- 1-2 hashtags max or none
- Visual content performs best; captions support the visual

---

## PHASE 2: CONTENT CREATION

### 2.1 Hook Writing Rules

The hook is the first line. It must do ONE of these:
- Make a bold/contrarian claim: "Most founders waste their first $10k on ads. Here's why."
- Ask a resonant question: "What if your clients never asked 'where are we on this?' again?"
- State a surprising fact: "The average SaaS buyer reads 27 pieces of content before a demo request."
- Make a direct promise: "3 email subject lines that doubled our open rate."
- Open a loop: "I made a mistake that cost us 3 months of growth. Here's what I learned."

**Bad hooks (do not use):**
- "Excited to share..." (nobody cares you're excited)
- "We're thrilled to announce..." (corporate non-hook)
- "In today's fast-paced world..." (filler opening)
- Emoji-as-hook with no substance

**Hook templates 1-15 (original):**

- Bold/contrarian claim: "Most founders waste their first $10k on ads. Here's why."
- Resonant question: "What if your clients never asked 'where are we on this?' again?"
- Surprising fact: "The average SaaS buyer reads 27 pieces of content before a demo request."
- Direct promise: "3 email subject lines that doubled our open rate."
- Open loop: "I made a mistake that cost us 3 months of growth. Here's what I learned."

**Hook templates 16-30 (extended library):**

16. Specific number that sounds wrong until explained: "We turned down $400k in ARR. Here's why."
17. Thing everyone does that doesn't work: "Every founder sends 'just following up' emails. They almost never work."
18. Belief flip: "I spent two years thinking better copy would fix conversion. I was wrong about what was broken."
19. The cut sentence: "Nobody says this out loud in B2B sales calls: most demos fail before the demo starts."
20. Specific day/moment: "Last Tuesday, a customer told us something that changed how we price."
21. Unexpected comparison: "Our best-performing email looked like 2004. No images, plain text. Converted 3x better."
22. Question with non-obvious implied answer: "Why do most enterprise deals stall at legal? (It's not what you think.)"
23. Failure that taught more: "The feature we're most proud of has a 4% adoption rate. That number taught us everything."
24. Counterintuitive rule from different domain: "Fighter pilots use OODA. I've been applying it to sales cycles."
25. Thing you stopped doing: "I stopped posting on LinkedIn for 60 days. Here's what happened to my pipeline."
26. Direct challenge to received wisdom: "The 4:1 content ratio is wrong for most B2B founders. Here's a better framework."
27. Scene setting up the problem: "The customer sat through a 45-minute demo, said 'looks great,' never replied. I've seen this 40 times."
28. Data contradicting headline: "10,000 outbound emails. Highest subject line: 12% open. Second: 11.9%. Everything else below 7%."
29. Say vs. do contrast: "Every founder says they want honest feedback. Almost none actually do. Here's how to tell."
30. Earned-attention number: "18 months, 3 pricing changes, firing first sales hire to figure this out."

Test every hook: Would you stop scrolling for this? If no, rewrite.

### 2.2 Post Structure by Format

**LinkedIn thought leadership post:**
```
[Hook - 1 sentence]

[Context / the problem - 1-2 sentences]

[The insight, lesson, or value - 3-5 short paragraphs]

[The takeaway or conclusion - 1-2 sentences]

[Question or CTA]

[Hashtags - bottom]
```

**X thread:**
```
Tweet 1: [Hook] + [Thread indicator: 1/N or "Thread:"]
Tweet 2-N: [Each tweet = one complete idea, can stand alone]
Final tweet: [Summary + CTA or link]
```

**Instagram caption:**
```
[Hook - must work in 125 chars]
[Elaboration - only visible after "more"]
[CTA: "Link in bio" or question]
[Hashtags]
```

**Short-form (Threads, casual X):**
```
[Single punchy observation, question, or take]
[Optional: 1 line elaboration]
[Optional: CTA]
```

### 2.2.5 Anti-AI-Slop Hook Audit

Before delivering any hook, check it against all 30 patterns below. If the hook matches any pattern, rewrite it.

**Phrases and structures that signal AI-generated content:**

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
24. Emoji structural signposts (checkmark X checkmark Y checkmark Z)
25. Hook where one word swap makes it apply to any other post
26. "The truth is..." followed by an obvious thing
27. Closing "What would you add?" when the post didn't earn a response
28. Fable-structure stories (no mess, no ambiguity, clean arc)
29. Universally-true advice ("communicate clearly")
30. Stats with no source, year, or methodology

If the hook matches any of these, rewrite it using a specific number, a real moment, a named contradiction, or a sentence only this brand/person could write.

### 2.3 Content Mix (for calendars and batches)

Apply the 4:1 rule by default (adjustable to user preference):
- **4 value posts:** Educate, entertain, inspire, share expertise
- **1 promotional post:** Product mention, CTA, offer, announcement

**Value post categories:**
- How-to / tactical tip
- Behind the scenes / process
- Industry insight or hot take
- Customer story / social proof
- Question / poll / community engagement
- Repurposed blog/content asset

**Promotional post categories:**
- Feature announcement
- Case study spotlight
- Trial/offer/discount
- Event/webinar promotion
- Direct demo/signup CTA

**Extended post types (beyond standard value/promo split):**

- **Teardown post:** Analyze a competitor's onboarding, pricing page, email, or product decision. "I signed up for [X]. Here's what they get right, what they get wrong, and what I'd steal." Screen recordings and annotated screenshots pair well with this format.
- **Primary research post:** Share data from a survey, poll, or CRM analysis the brand ran. "I asked 47 [ICP role] about X. Here's what they actually said." LinkedIn polls with 100+ responses are citable. Typeform surveys to existing customers (5-7 questions) generate repeatable content.
- **Honest update post (Arvid Kahl model):** Transparent report on revenue, failures, a mistake made, or a number that didn't go as planned. Specific numbers ("We lost 23% MRR in six weeks") earn trust in a way that polished announcements never do. No spin, no reframe.
- **Belief-flip post:** "I used to believe X. Here's what changed my mind." Requires a named belief, a specific event or data point that challenged it, and a concrete new behavior (not just a new opinion).

For any calendar, map each post to a category and verify the 4:1 ratio is met.

### 2.5 Voice Fingerprint Rule

Every post must contain ONE sentence that only this brand or person could write - drawn from specific experience, a named decision, a real number, or a situation no one else lived through.

Generic observations without specificity are drafts, not finished posts. Before delivering any post, identify the voice fingerprint sentence and confirm it could not have been written by a different brand or creator. If no such sentence exists, add one or flag the post as a draft requiring the author's specific input.

### 2.4 Variant Generation

For every post, generate at minimum:
- **Variant A:** Pain-led angle (leads with the problem)
- **Variant B:** Outcome-led angle (leads with the result)

For paid social or high-stakes posts, add:
- **Variant C:** Social proof angle (leads with a customer result or stat)

---

## PHASE 3: CALENDAR OUTPUT (if requested)

### 3.1 Calendar Format

If the user asked for a calendar (weekly, monthly, etc.), output in this format:

```markdown
## [Month/Week] Content Calendar - [Platform(s)]

### [Date - Day]
**Platform:** [platform]
**Format:** [post type]
**Category:** [value / promotional]
**Hook:** [first line of the post]
**Full post:** [complete post text]
**Notes:** [image direction, link to include, etc.]

---
```

### 3.2 Calendar Cadence by Platform

Recommended default posting frequencies:
- LinkedIn: 3-5 posts/week (quality over volume)
- X: 1-5 posts/day (high volume, lower effort per post acceptable)
- Instagram: 4-7 posts/week (mix of feed, reels, stories)
- Threads: 1-3 posts/day
- TikTok: 1-2 posts/day minimum for algorithm traction

If the user specifies a different frequency, use it. If they say "daily," confirm which platform.

### 3.3 Theme Weeks (optional)

For monthly calendars, consider organizing around weekly themes:
- Week 1: Education / how-to focus
- Week 2: Community / engagement focus
- Week 3: Behind the scenes / brand story
- Week 4: Social proof + promotional

---

## PHASE 4: REVIEW & DELIVERY

Present all posts grouped by platform. For each post, show:
- Platform
- Post type / format
- Category (value / promotional)
- Full post text (ready to copy-paste)
- Variant B alongside Variant A (clearly labeled)

Ask: "Want me to adjust tone, swap any angles, or produce posts for additional platforms?"

Save all output to `.social-reports/[slug]/`.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Social-Specific Cleanup

1. All content saved to `.social-reports/[slug]/` - already gitignored
2. No servers, browsers, or APIs opened - nothing to close
3. WebFetch sessions (for URL research) are stateless - no cleanup needed

---

## SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```
═══════════════════════════════════════════════════════════════
SITREP — SOCIAL CONTENT
═══════════════════════════════════════════════════════════════

  Product/Brand:  [name]
  Platforms:      [list]
  Posts Created:  [count] across [N] platforms
  Variants:       [A/B per post: yes/no]
  Calendar:       [N days / "single batch"]
  Content Mix:    [ratio - e.g., 8 value : 2 promo]

  Files:
    [platform].md files in .social-reports/[slug]/posts/
    [CALENDAR.md if calendar mode]

  Status:         READY_TO_PUBLISH

═══════════════════════════════════════════════════════════════
                    Next: Schedule or post manually
═══════════════════════════════════════════════════════════════
```

---

## RELATED SKILLS

**Feeds from:**
- `/marketing` - the approved messaging framework, ICP, and channel plan are the source of truth for social content strategy
- `/copy` - hook copy and CTA copy from landing pages can be adapted into social hooks
- `/blog` - every published article should be distributed as social posts; run `/social` after `/blog` completes
- `/brainstorm` - for campaign ideation before writing; Socratic exploration finds the right angle

**Feeds into:**
- (none - /social is typically a terminal deliverable, ready for manual scheduling)

**Pairs with:**
- `/marketing` - run together when launching a campaign that needs both strategy and immediate social output
- `/blog` - pair to create article + social distribution in one session

**Auto-suggest after completion:**
When this skill finishes successfully, suggest: scheduling posts through your preferred social scheduler (Buffer, Later, Hootsuite, or native platform schedulers)

---

## REMEMBER

- **Platform-native always** - LinkedIn is not X is not Instagram; adapt every post
- **Hook or die** - if the first line doesn't earn the scroll-stop, rewrite it
- **4:1 value-to-promo ratio** - never flip this; algorithmic reach depends on it
- **Variants are required** - one angle is not testing; two angles is the minimum
- **Save everything** - unused post ideas go in content-ideas.md for future use

---

## PLATFORM INTELLIGENCE: 2026 UPDATE

### LinkedIn Advanced Features

**LinkedIn Newsletter (built-in feature):**
LinkedIn newsletters notify all subscribers via in-app notification AND email on publish. This produces 40-60% open rates vs. 20% typical for standalone email newsletters.

When to use LinkedIn newsletter vs. standalone (Beehiiv/Kit):
- **Use LinkedIn newsletter** when: your audience is primarily LinkedIn-native, you are building an audience from zero, and you are not yet doing email marketing. LinkedIn newsletter subscribers are platform-locked - you cannot export them.
- **Use standalone** when: you want to own your list, need segmentation, run commercial sequences, or have an audience that includes non-LinkedIn users. The list is an asset you own.
- **Hybrid (recommended for established brands):** Publish on LinkedIn for discovery, gate the full archive and deep content on standalone. LinkedIn newsletter = top-of-funnel, email = owned relationship.

Important limitation: LinkedIn does not allow subscriber list export. Platform risk is real - if you build purely on LinkedIn newsletters, you do not own that audience.

**LinkedIn Live:**
When to run a LinkedIn Live session:
- You have a named expert or guest with an existing LinkedIn audience (their notification to followers drives registration)
- The topic can sustain 30-45 minutes of unscripted conversation (product demos, expert interviews, or hot-takes work - prepared slide decks do not)
- You can promote it 5-7 days in advance with teaser posts
- You have a moderator separate from the speaker to handle live questions

Optimal structure:
1. Hook in the first 2 minutes - state the specific thing viewers will walk away knowing
2. Guest/expert credibility in 60 seconds - specific, not bio-recitation
3. Meat (20-25 minutes) - conversation, not presentation
4. Live Q+A (10-15 minutes) - pre-seed with 2-3 questions in case live questions are slow to start
5. CTA - one, specific, stated once

Post-live: record auto-saves to LinkedIn. Clip the best 60-90 seconds into a standalone post within 24 hours.

### X (Twitter) in 2026

**X Premium features (Premium and Premium+ tiers):**
- Long-form posts: up to 25,000 characters (effectively blog-length articles native to X)
- Subscriber-only content: posts visible only to paying subscribers - useful for exclusive analysis or behind-the-scenes
- Revenue share: available for Premium+ accounts meeting follower/engagement thresholds

When to use long-form X posts:
- Your audience is power users who live in X (founders, investors, tech operators)
- The content is analysis-heavy and benefits from X's notification and quote-tweet mechanics
- You have existing engagement signal on X (low-follower accounts get low reach on long-form regardless)

Do not use X long-form if: your audience engagement is under 2% on standard posts. Fix reach first.

**Algorithm signals (2026):**
- Replies drive reach more than likes - a post with 10 replies outperforms a post with 100 likes in feed distribution
- Quote tweets from accounts with engaged audiences are the highest-leverage amplification signal
- Avoid: external links in the body of the tweet (reduces reach). Put the link in the first reply instead.

### Threads vs. X: Decision Framework

Source: Buffer Q1 2025 dataset, OktoPost B2B Threads analysis, Sendible platform comparison.

| Factor | Threads | X |
|--------|---------|---|
| Monthly active users | 400M (fast growing) | 600M (stable) |
| Engagement rate (median) | 6.25% | 3.6% |
| Audience tone | Collaborative, community-building | Debate, hot takes, news |
| B2B content fit | Thought leadership, community | Real-time commentary, links |
| Algorithm basis | Engagement quality + topic relevance | Engagement bait + controversy |
| Cross-post from Instagram | Yes, native (but adapt the copy) | No |
| Hashtags | 1 topic tag per post - helps reach | 0-1 - more hurts reach |
| Links | Fine in posts | Put in first reply for best reach |

**When to go native (not cross-post):**

Threads native: community conversation starters, opinions on B2B topics where you want genuine replies, anything where the goal is building a specific professional community. Threads penalizes content that reads like an ad or announcement.

X native: real-time commentary on news/events, industry hot takes, technical content for an operator audience, anything that benefits from the quote-tweet dynamic.

**Cross-posting rules:**
- Never post the same caption to both - adapt tone and length
- If cross-posting, post Threads first (lower stakes), then adapt the strongest response-driver for X
- Remove all hashtags when cross-posting to X (where they hurt reach)

### TikTok and Reels for B2B

Source: Sotrender TikTok B2B strategy 2025, Manchester Digital B2B TikTok analysis, HubSpot B2B decision-maker TikTok data.

67% of B2B decision-makers use TikTok for information gathering (HubSpot 2025 data) - a 22% increase year-over-year. Gen Z buyers search TikTok before Google for "how it works" content.

**What actually works for B2B (not generic TikTok advice):**

- **"How it works" demos:** Short (60-90 second) product walkthroughs showing a specific problem being solved. Not a feature tour - one problem, one solution, visible result. This is TikTok's highest-performing B2B format because it matches how buyers research independently.
- **Behind-the-scenes process:** Show the real work - not inspirational content about your culture. Show the actual process of building, shipping, or solving something. Specificity drives engagement.
- **POV: You're a [ICP role]:** First-person narrative of a day in the life of your buyer. Effective for awareness because it gets shared within the buyer's own community.
- **Data/stat hooks:** Lead with a counterintuitive number. "73% of [ICP] do X the wrong way. Here's what works instead." TikTok's algorithm favors content that generates saves and shares - data-driven hooks drive both.

**What does NOT work for B2B TikTok:**
- Trend-chasing (dancing to audio that has nothing to do with your product)
- Polished corporate production (TikTok rewards authenticity over production quality)
- Announcement-style content ("We're excited to share...")
- Long talking-head explanations over 90 seconds without a visual hook

**Reels vs. TikTok for B2B:**
Identical content strategy - produce once for TikTok, repurpose to Reels with identical or slightly tweaked caption. The algorithm behaviors are similar enough that native-first TikTok content performs well on Reels without editing.

**B2B TikTok posting guidance:**
- 1 post/day for algorithm traction (minimum for discovery)
- First 3 seconds: the specific problem or counterintuitive claim - no logos, no intros, no "hey guys"
- Captions: 150 chars max - they are secondary to the video. Write the caption after the video is done, not before.
- Hashtags: 3-5 including 1-2 niche industry tags (e.g., #saasfounder, #b2bmarketing) and 1 broad (#startup)

### Algorithm Signals by Platform (2026)

| Platform | Top Signal | Secondary Signal | Kill Signal |
|----------|-----------|-----------------|-------------|
| LinkedIn | Dwell time (do they read the whole post?) | Comments with substance (3+ words) | Outbound links in post body |
| X | Replies and quote-tweets | Bookmarks | External links in tweet body |
| Threads | Reply depth (do people respond to responses?) | Shares outside the platform | Hashtag overuse |
| Instagram | Saves and shares to stories | Comments | Follower-gated engagement bait |
| TikTok | Watch completion rate + shares | Saves | Skip-rate in first 3 seconds |

**Posting time research (Central Time):**
- LinkedIn: Tue/Wed/Thu, 8-10am CT and 5-6pm CT. Weekend posts get 35% less organic reach.
- X: Mon-Fri, 9am-12pm CT and 5-8pm CT for maximum initial velocity. Threads do well at 8-9am CT.
- Threads: Tue-Thu, 9am-11am CT. Avoid Friday afternoons.
- TikTok B2B: Tue/Thu/Fri, 7-9am CT and 7-9pm CT (commute + evening browsing windows)
- Instagram: Tue-Fri, 9am-11am CT and 6-8pm CT

**Dwell time targets (LinkedIn-specific):**
- Target 30+ seconds of dwell per post (this is what triggers extended distribution)
- Posts under 150 characters: low dwell, low reach
- Posts with line breaks and white space: higher dwell (easier to read = more time spent)
- Native documents (carousels, PDF posts): highest average dwell time on LinkedIn

ARGUMENTS: $ARGUMENTS

<!-- Claude Code Skill — Generic social media content pipeline, works for any product/platform/industry -->
<!-- Part of the Claude Code Skills Collection -->
<!-- License: MIT -->
