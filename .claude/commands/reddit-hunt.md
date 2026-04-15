---
description: "/reddit-hunt - Reddit-specific prospect finder. Reads icp.json, searches relevant subreddits for self-identified pain signals, scores posts, drafts value-first replies. Complements /prospect with a channel-specific discovery loop."
allowed-tools: Bash(curl *), Bash(jq *), Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Bash(sleep *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /reddit-hunt - Reddit Prospect Discovery Pipeline

**Steel Principle: No Reddit DM, ever. Value is given publicly in the thread, or not at all.** Reddit's unwritten rules are non-negotiable: users who DM prospects they found on Reddit get banned, get reported, and damage the product's reputation permanently. This skill produces reply drafts meant to be posted as public comments, not private messages.

A discovery loop for finding prospects on Reddit who have **self-identified** the pain your product solves. Takes an ICP (from `/prospect`) and a list of target subreddits, queries the Reddit public JSON API for recent posts matching pain keywords, scores each post for fit and urgency, and drafts value-first public replies ready for user review.

> **When to use /reddit-hunt:**
> - You have an ICP and want Reddit-native prospects, not Apollo contacts
> - You want conversations happening NOW (last 7-30 days), not cold lists
> - You are in a B2B vertical where Reddit is active (devtools, SaaS, agencies, content creators, productivity, crypto, DTC)
> - You want prospects who have ALREADY described the pain in their own words
>
> **When NOT to use /reddit-hunt:**
> - You need verified business emails and titles - use `/prospect` with Apollo
> - You need bulk outreach at scale - Reddit is quality-over-quantity
> - Your ICP is enterprise IT buyers - they are rarely on Reddit in professional capacity
> - You want the copy for the reply itself optimized - draft here, then pass to `/copy` for polish

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

**Steel Principle #1:** No DMs to prospects found on Reddit. Public thread reply only. Violating this gets the account banned and earns a reputation that cannot be repaired.

**Steel Principle #2:** Value is given before the product is mentioned. The first 80% of the reply answers the OP's question fully; the mention (if any) is an incidental "also, if this becomes a pattern, tools like X help" in the final line.

**Steel Principle #3:** Respect subreddit rules. Every subreddit has a pinned rule set; the skill reads them before drafting replies. Subreddits that ban self-promotion entirely get flagged as "read-only observation" - watch for signal, do not engage commercially.

**Steel Principle #4:** The Reddit public JSON API (no auth required for read) has rate limits. Respect them. The skill enforces a 2-second minimum delay between requests and caps any single run at 60 requests.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "A DM feels more personal" | Reddit users treat uninvited DMs as spam. Report rate is ~30% | Public comment only. If they want a DM, they DM you |
| "Self-promo once is fine" | Most subs have a 9:1 or 10:1 ratio rule (value posts to promo). Breaking it tanks karma and gets posts auto-removed | Read the sub's pinned rules. If you haven't given 9 value posts, don't promote |
| "I'll mention the product in the opening line" | The algorithm downranks and moderators remove promotional replies | Put the product mention at the END, after substantive value, and only if directly relevant |
| "I can hit 50 subreddits in a single run" | Subreddit quality drops off fast after your top 5-10 targets | Cap the run at 5-10 subreddits, iterate |
| "The post is from 3 months ago but matches perfectly" | Stale threads don't convert; OPs stopped caring or already solved it | Default filter: last 7 days. Max: last 30 days. Archive after 30. |
| "Reddit doesn't matter for B2B" | For devtools, SaaS, agencies, and DTC, Reddit has the best CPL in B2B ($3-12 vs $50-150 on LinkedIn) | For the right vertical, Reddit is the primary channel, not secondary |
| "The karma filter is optional" | Accounts with less than 100 karma have posts auto-filtered in most subs | Your account must have 100+ karma in the target sub BEFORE posting value replies |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Session folders live in gitignored `.reddit-hunt-reports/`
- Reddit API response cache lives in gitignored `.reddit-hunt-cache/` (24h TTL)
- No cookies or auth tokens are written to disk (public API only)
- Draft replies are written to the session folder; NEVER auto-posted

---

## WHEN TO USE /reddit-hunt vs. /prospect

| Use /reddit-hunt | Use /prospect |
|------------------|---------------|
| Finding people WHO described the pain publicly | Finding people who FIT the ICP |
| Warm leads (already hurting) | Cold leads (theoretically fit) |
| Signal: "I need X" / "anyone know a tool for Y" | Signal: title match + company size |
| Output: public reply draft | Output: email sequence |
| CPL $3-12 (public channel) | CPL $20-80 (paid data) |

They are complementary. Best practice: run `/prospect` to define the ICP, then `/reddit-hunt` to find the people who already need it.

---

## SESSION FOLDER STRUCTURE

```
.reddit-hunt-reports/YYYYMMDD-HHMMSS-<icp-slug>/
  interview-answers.md       (Phase 0)
  hunt-plan.md               (Phase 2)
  subreddit-rules/
    r-<name>-rules.md        (scraped rules per sub)
  raw-posts/
    r-<name>-posts.json      (raw API response)
  scored-posts.md            (ranked matches)
  draft-replies/
    post-<id>.md             (one reply per high-scoring post)
  subreddit-insights.md      (terminology, active hours, top posters)
  sitrep.md
```

Cache:
```
.reddit-hunt-cache/
  r-<name>-<query-hash>.json   (24h TTL)
  robots-txt-check.json         (Reddit robots.txt cached)
```

Setup adds these to `.gitignore`:
```bash
grep -q ".reddit-hunt-reports" .gitignore 2>/dev/null || echo ".reddit-hunt-reports/" >> .gitignore
grep -q ".reddit-hunt-cache" .gitignore 2>/dev/null || echo ".reddit-hunt-cache/" >> .gitignore
```

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these six questions. Wait for answers before running.

**Q1:** "Which ICP am I hunting for? (Point me to `.marketing-context/icp.json` or describe the target in one line. If no ICP exists, stop and run `/prospect` first.)"

**Q2:** "Which subreddits are the watering holes? (Give me 3-10 subreddit names. If you don't know, I can suggest some from the ICP's domain; confirm before I hit the API.)"

**Q3:** "What are the pain keywords prospects use when describing the problem? (Think in their words, not yours. Examples: 'drowning in notifications', 'can't keep up with emails', 'losing track of bills'. 5-15 keywords ideal.)"

**Q4:** "Time window: last 24 hours, last week, last month? (Default: last 7 days. Older posts rarely convert.)"

**Q5:** "Mode: discover (one-shot scan, rank top matches) or monitor (recurring scan, deliver fresh matches daily)? (Default: discover for first run.)"

**Q6:** "Is there a specific Reddit account I should check karma for before drafting replies? (If yes, I will flag subs where your account is below the posting threshold.)"

Display a summary and confirm before Phase 1.

---

## PHASE 1: CONTEXT LOAD

**Setup:**
```bash
mkdir -p .reddit-hunt-reports
mkdir -p .reddit-hunt-cache
grep -q ".reddit-hunt-reports" .gitignore 2>/dev/null || echo ".reddit-hunt-reports/" >> .gitignore
grep -q ".reddit-hunt-cache" .gitignore 2>/dev/null || echo ".reddit-hunt-cache/" >> .gitignore
```

**Load ICP context:**
- Read `.marketing-context/icp.json` (from `/prospect`) if present
- Extract: tier A titles, pain points, trigger events, negative ICP (for filtering)

**Robots.txt check:**
```bash
curl -s -A "Mozilla/5.0 (compatible; icp-prospect-bot)" "https://www.reddit.com/robots.txt" | head -50
```
Reddit allows read access to the JSON endpoints for public content. Confirm the rules haven't changed by caching the result in `.reddit-hunt-cache/robots-txt-check.json`.

**Subreddit rules scan:**
For each target subreddit, fetch the rules page and save:
```bash
curl -s -A "Mozilla/5.0 (compatible; icp-prospect-bot)" \
  "https://www.reddit.com/r/<name>/about/rules.json" \
  -o ".reddit-hunt-cache/r-<name>-rules.json"
sleep 2
```

Parse rules for:
- Self-promotion policy (banned / ratio-limited / allowed with flair)
- Minimum karma or account age thresholds
- Banned topics
- Required post flair

Flag any sub that bans self-promo outright as **observation-only**.

---

## PHASE 2: PLAN + HARD GATE

Write `hunt-plan.md`:

- **ICP summary:** one-paragraph digest
- **Subreddits targeted:** list with engagement class (engage / observe-only / skip) per sub
- **Keywords to search:** final list (post-refinement)
- **Time window:** last N days
- **Expected volume:** approx. posts per sub per day (heuristic)
- **Scoring rubric:** (Phase 3) explicit point values
- **Draft reply style:** formal / conversational / technical / friendly

**HARD GATE:**

```
═══════════════════════════════════════════════════════════════
HUNT PLAN REVIEW - /reddit-hunt
═══════════════════════════════════════════════════════════════

  ICP:              [one-line digest]
  Subreddits:       [N engage, N observe-only]
  Keywords:         [N terms]
  Window:           [last N days]
  Mode:             [discover / monitor]
  Est. API calls:   [N, within rate limit]

  Proceed? Approve, or tell me what to change.
═══════════════════════════════════════════════════════════════
```

Do not proceed until the user approves.

---

## PHASE 3: EXECUTE

### 3.1 Reddit Public API Query Loop

For each (subreddit, keyword) pair:

```bash
QUERY_ENCODED=$(echo "$KEYWORD" | jq -sRr @uri)
curl -s -A "Mozilla/5.0 (compatible; icp-prospect-bot)" \
  "https://www.reddit.com/r/${SUB}/search.json?q=${QUERY_ENCODED}&restrict_sr=1&sort=new&t=week&limit=25" \
  -o ".reddit-hunt-cache/r-${SUB}-${QUERY_HASH}.json"
sleep 2
```

Rate limit enforcement:
- 2 seconds between requests minimum
- Cap: 60 requests per run (covers 10 subs x 6 keywords)
- On HTTP 429 response, exponential backoff: 30s, 60s, 120s, then abort run
- Cache hits (24h TTL) do not count against rate limit

### 3.2 Post Scoring

For each post returned, apply the scoring rubric:

| Signal | Points | Source |
|--------|--------|--------|
| Title contains a pain keyword | +10 | title match |
| Self-text describes specific pain (not vague complaint) | +15 | self-text analysis |
| Post asks an explicit question ("does anyone know...", "how do you...") | +10 | question mark + query pattern |
| Post is under 48 hours old | +10 | created_utc |
| Post has 5+ comments (active discussion) | +5 | num_comments |
| OP account has 1000+ karma (real user, not spam) | +5 | author karma |
| Post mentions a budget ("willing to pay", "budget for", "subscribed to [competitor]") | +15 | phrase match |
| Post mentions a competitor by name | +10 | ICP negative list cross-check |
| Post contains a disqualifier (ICP negative signal) | -20 | negative ICP match |
| Post is a meme / joke / off-topic | -15 | low-content heuristic |
| OP account is less than 30 days old | -10 | created_utc of account |

**Tiers:**
- **Tier 1 (reply now):** score >= 40
- **Tier 2 (monitor):** score 20-39
- **Tier 3 (skip):** score < 20

### 3.3 Subreddit Insights Extraction

For each target sub, build a terminology map:
- Top 10 recurring terms in the last 50 posts
- Active hours (time distribution of posts)
- Top 5 frequent posters (for relationship-building)
- Common post templates (Question / Showcase / Rant / Help-request)

Write to `subreddit-insights.md`. This is gold for future `/copy` runs targeting the same audience.

### 3.4 Value-First Reply Drafts

For every Tier 1 post, generate a draft reply following this structure:

```
[Acknowledge the pain in 1 sentence, show you actually read the post]

[Provide the substantive answer or framework in 2-4 sentences.
 This is the value. If the reader does nothing else, they learned
 something useful. The value must be complete without your product.]

[Optional single-line mention IF AND ONLY IF directly relevant:
 "FWIW, if this becomes a recurring pattern, tools like {{product_name}}
 automate this specific loop - no affiliation obligation, just mentioning
 because it fits your use case."]

[Close with a question or an invitation to follow up, to keep the
 conversation going if they want.]
```

Rules encoded in the draft:
- Never lead with the product
- Never say "I work on" unless the sub explicitly allows disclosed self-promo
- Never link to the product in the first reply (link to a free resource or doc instead)
- Match the sub's register (technical subs get technical answers; casual subs get casual tone)
- Under 200 words; Reddit readers abandon long replies

Save each draft to `draft-replies/post-<id>.md` with:
- Post title + link
- OP username + karma
- Score breakdown
- Draft reply text
- Recommended flair (if sub requires)
- Timing recommendation (post within N hours for best visibility)

### 3.5 Disclosure Guardrails

If ANY draft reply mentions the product:
- Add a `## DISCLOSURE CHECK` section warning the user
- Remind them of the target sub's self-promo rules
- Suggest waiting to reply until they have 3+ value-only replies in that sub first

---

## PHASE 4: VERIFY

Before handing drafts to the user:

1. **Lint check each draft reply:**
   - Reply length: 50-200 words (flag outliers)
   - Product mention count: 0 or 1 (flag if 2+)
   - First sentence does NOT mention product
   - Links (if any) go to a free / public resource, not a pricing page

2. **Rule compliance check:**
   - Does the draft comply with the target sub's rules? Cross-reference Phase 1 rules scan
   - If sub bans self-promo, strip product mention entirely

3. **Send summary for user review:**
   - "{{N}} Tier 1 posts found. Drafts ready. Review order: [ranked list by score]. Any you want me to tighten before you post?"

---

## PHASE 5: SITREP + HANDOFF

Reference `~/.claude/standards/SITREP_FORMAT.md`.

Domain-specific SITREP additions:
- **Posts scanned:** `{{N}}` across `{{M}}` subreddits
- **Tier 1 matches (reply now):** `{{N_tier_1}}`
- **Tier 2 matches (monitor):** `{{N_tier_2}}`
- **Subs flagged observe-only:** `{{list}}`
- **Drafts produced:** `{{N_drafts}}` in `.reddit-hunt-reports/.../draft-replies/`
- **Subreddit insights captured:** `{{M}}` terminology maps
- **Next action:** Review drafts, post to Reddit manually (skill never auto-posts)

Close with:
```
Hunt complete.

→ Review drafts in .reddit-hunt-reports/{{folder}}/draft-replies/
→ Want me to polish the top 3 with /copy?
→ Want to convert Tier 2 observations into a nurture list in /prospect?
```

---

## MONITOR MODE

If the user selected "monitor" in Phase 0, set up a recurring hunt pattern:

- Save the interview answers as `.marketing-context/reddit-hunt-profile.json`
- Schedule a daily run (via `/schedule` skill or cron)
- Each run appends new matches to a running ledger; does not re-draft old matches
- Delivers a daily digest: "Today's new Tier 1 matches: [N]. Highest score: [post]. Recommended reply: [link to draft]."

---

## SELF-IMPROVEMENT HOOKS

Telemetry to log (append to `.reddit-hunt-reports/telemetry.jsonl`):
- `duration_seconds`
- `api_calls_made`
- `cache_hit_rate`
- `posts_scanned`
- `tier_1_matches`
- `tier_2_matches`
- `user_post_rate` (how many drafts were actually posted - user reports back)
- `reply_conversion_rate` (how many replies led to DMs IN from the OP)

If `tier_1_matches` is consistently zero, the keyword set is miscalibrated; flag in SITREP.

---

## RELATED SKILLS

**Feeds from:** `/prospect` (reads `.marketing-context/icp.json`), `/icp-from-repo` (indirect, via `/prospect`)

**Feeds into:** `/copy` (polish top draft replies), `/social` (subreddit insights inform LinkedIn/X content), `/campaign` (Reddit channel as part of multi-channel GTM)

**Pairs with:** `/scrape` (scrape competitor mentions in Reddit threads for positioning), `/narrative` (high-scoring post patterns inform storytelling angles)

**Auto-suggest after completion:**
- "Want me to polish the top 3 drafts with `/copy`?"
- "Want to expand to LinkedIn with `/social` using the same pain language?"
- "Set up daily monitoring mode?"

---

## PATTERN

Pattern A (Socratic + Plan + Gate + Verify + SITREP) per [Skill Pattern Standard](~/.claude/standards/SKILL_PATTERN.md).
