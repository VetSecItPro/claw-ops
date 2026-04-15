---
description: "/hunt - Cross-channel pain-signal prospect discovery. Reads icp.json, scans Hacker News, GitHub Issues, Reddit (via SearXNG), and Indie Hackers (via SearXNG site: queries) for self-identified pain matching the ICP, scores signals on a unified rubric, drafts value-first replies. Channel backends are pluggable."
allowed-tools: Bash(curl *), Bash(jq *), Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Bash(sleep *), Bash(sed *), Bash(awk *), Bash(grep *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /hunt - Cross-Channel Pain-Signal Prospect Discovery

**Steel Principle: No DM, ever, from a signal surfaced here. Value is given publicly in the thread, or not at all.** Across every channel - Hacker News, GitHub, Reddit, Indie Hackers - cold DMs from someone who found you in a public thread are spam. They damage reputation permanently. This skill produces drafts for public reply, never private outreach.

A unified discovery loop. Takes an ICP (from `/prospect`) and pain keywords; sweeps multiple public channels for prospects who have **already described the pain in their own words**; scores signals on one rubric so cross-channel comparisons hold; drafts value-first replies. Replaces the channel-specific `/reddit-hunt` skill - new channels plug in as additional Phase 2 sub-routines without rewriting the skill.

> **When to use /hunt:**
> - You have an ICP and want prospects expressing the pain NOW (last 7-30 days)
> - You want voice-of-customer language for `/copy` / `/social` / `/marketing`
> - Your audience lives in public communities (devtools, SaaS, agencies, productivity, DTC, indie founders)
> - You want comparable scores across channels, not channel-by-channel silos
>
> **When NOT to use /hunt:**
> - You need verified business emails - use `/prospect` with Apollo
> - You need the polished outreach copy - draft signals here, then pass to `/outreach` (when built) or `/copy`
> - Your ICP is enterprise IT buyers - they rarely surface pain publicly; use `/prospect` instead

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

**Steel Principle #1 (no DMs, no auto-post):** Public reply only. Drafts are reviewed by the user before posting. The skill never posts on its own.

**Steel Principle #2 (value before product):** First 80% of every draft answers the OP fully. The product mention (if any) is incidental, in the closing line, only when directly relevant.

**Steel Principle #3 (channel rules respected):** Each channel has its own etiquette - subreddit pinned rules, HN guidelines, GitHub maintainer norms, Indie Hackers community rules. The skill flags any signal where a draft would violate channel norms.

**Steel Principle #4 (one rubric across all channels):** Cross-channel scores are only useful if they mean the same thing. Every channel scores against the same 0-100 rubric (pain-severity, ICP-fit, recency, engagement). Drift breaks the comparison.

**Steel Principle #5 (no hallucinated signals):** Every scored signal must have a real artifact on disk - the API/HTML response that produced it. No score without a corresponding cache file. Verification (Phase 4) checks this invariant.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "This channel feels noisy, skip it" | The noisy channel is often the highest-signal one (Reddit and HN both feel noisy until you ICP-filter them) | Run all approved channels. Let the score rank, not the gut. |
| "Just hit the Reddit JSON API, it's cleaner than scraping" | Reddit's robots.txt is `Disallow: /` and API access is tightly gated since 2023. Direct hits risk IP ban and ToS violation | Use SearXNG (or Google site: search) as a proxy for Reddit content. Never hit Reddit JSON endpoints directly for search or listings. |
| "GitHub Issues is for bug reports, not prospects" | Maintainers and users posting "I keep hitting X, this should be a real product" ARE prospects expressing pain | Search for problem language, not feature requests. Filter for non-bug threads. |
| "Indie Hackers is too small to matter" | IH posts ARE indie founder pain in their own words - exactly the VoC source we want for SaaS messaging | Even 3 IH signals per run inform copy disproportionately |
| "I'll DM the OP since the thread already exposed them" | Cold DMs from public-thread discovery are spam. ~30% report rate. Account bans across HN/Reddit/IH | Public reply only. They DM you if interested. |
| "This 60-day-old post matches perfectly" | Stale threads don't convert. OP solved the problem or moved on | Default 7 days. Max 30. Skip older. |
| "I can hunt 20 ICPs in one run" | Mixing ICPs corrupts scoring (a Tier A signal for ICP-A is Tier C for ICP-B) | One ICP per run. Re-invoke for additional ICPs. |
| "The cross-channel rubric is overkill, score per-channel" | Per-channel scoring forces user to re-rank manually. Defeats consolidation | Same rubric, same weights, every channel. Period. |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md).

```markdown
🚀 /hunt Started
   ICP:         [one-line digest]
   Channels:    [HN, GitHub, Reddit, IndieHackers]
   Window:      last [N] days

🔍 Phase 1: ICP + Keyword Load
   ├─ icp.json read: [path]
   ├─ Pain keywords: [N]
   └─ ✅ Load complete

🎯 Phase 2: Backend Sweep (per-channel sub-routines)
   ├─ HN (Algolia)         → [N raw, M after-filter]
   ├─ GitHub Issues         → [N raw, M after-filter]
   ├─ Reddit (SearXNG)      → [N raw, M after-filter]
   └─ Indie Hackers (SearXNG)→ [N raw, M after-filter]
   └─ ✅ [TOTAL] signals collected

🧮 Phase 3: Unified Scoring
   ├─ Tier 1 (>= 60): [N]
   ├─ Tier 2 (30-59): [N]
   └─ Tier 3 (< 30): [N - filtered]

📝 Phase 4: Drafts + Verify
   ├─ [N] drafts written
   └─ ✅ All cross-checked against backing artifact

📊 Phase 5: SITREP + handoff
```

**Autonomy rule:** After Phase 1 confirms the ICP loaded, Phases 2-5 run end-to-end. Only HARD GATE is in Phase 1 if `icp.json` is missing - skill stops and tells user to run `/prospect` first. No mid-run confirmations otherwise.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

- Session output lives in gitignored `vault/leads/hunt/<date>/` (canonical) plus session scratch in `.hunt-cache/` (24h TTL)
- No cookies or auth tokens written to disk - all backends are unauthenticated public APIs (HN Algolia, GitHub anon search, SearXNG localhost)
- Drafts NEVER auto-posted - hard invariant
- Setup adds these to `.gitignore`:
  ```bash
  grep -q "^.hunt-cache" .gitignore 2>/dev/null || echo ".hunt-cache/" >> .gitignore
  grep -q "^vault/leads/hunt" .gitignore 2>/dev/null || echo "vault/leads/hunt/" >> .gitignore
  ```

---

## PHASE 1: ICP + KEYWORD LOAD (HARD GATE if missing)

Read `vault/marketing/icp.json` (or `.marketing-context/icp.json` legacy path). Required fields:

- `tier_a_titles`, `pain_points`, `negative_icp`, `trigger_events`
- Pain keywords (the language THEY use, not us)

```bash
# Why we hard-gate here: hunting without an ICP produces noise scored against nothing.
# Every other phase depends on these fields to filter and score.
ICP_PATH="vault/marketing/icp.json"
[ ! -f "$ICP_PATH" ] && ICP_PATH=".marketing-context/icp.json"

if [ ! -f "$ICP_PATH" ]; then
  echo "❌ No icp.json found. Run /prospect first to define the ICP."
  exit 1
fi

PAIN_KEYWORDS=$(jq -r '.pain_keywords[]? // .pain_points[]?' "$ICP_PATH")
NEGATIVE_TERMS=$(jq -r '.negative_icp[]? // empty' "$ICP_PATH")
```

If the user did not provide pain keywords explicitly, derive 5-15 candidate keywords from `pain_points` and confirm with the user before continuing.

---

## PHASE 2: BACKEND SWEEP

### Plug-In Contract

Every channel backend MUST return a JSON array of signals with this exact shape - this is the contract that lets `/hunt` add new channels (LinkedIn Pulse, Product Hunt, G2, Capterra) in v2 without rewriting scoring or output:

```json
{
  "channel": "hn|github|reddit|indiehackers",
  "id": "<channel-native-id>",
  "url": "<canonical permalink to the signal>",
  "title": "<post or thread title>",
  "author": "<username if observable, else null>",
  "body": "<post body / question text / issue body, plain text>",
  "created_utc": "<ISO 8601 timestamp>",
  "engagement": {
    "comments": <int or null>,
    "score": <int or null>,
    "reactions": <int or null>
  },
  "raw_artifact_path": "<path to cached raw response that produced this row>"
}
```

`raw_artifact_path` is the verification anchor (Steel Principle #5): every signal must point to a real file on disk that produced it.

### 2.1 Hacker News Backend (Algolia API)

**Why Algolia, not the firebase HN API:** Algolia's `hn.algolia.com/api/v1/search` exposes free-text + tag filtering across all HN content (stories, comments, Ask HN), no auth, generous rate limits. The Firebase API is item-id-based - useless for keyword discovery.

```bash
# HN Algolia: zero auth, 10k+ rpm public limit. We use comment search because pain
# language usually surfaces in comments, not story titles.
HN_OUT="$SESSION_DIR/raw/hn-${KEYWORD_HASH}.json"
curl -s "https://hn.algolia.com/api/v1/search?query=$(jq -nr --arg q "$KEYWORD" '$q|@uri')&tags=comment&hitsPerPage=20" \
  > "$HN_OUT"
sleep 1  # courteous, not required by limits

# Normalize to the plug-in contract:
jq --arg ch "hn" --arg artifact "$HN_OUT" '
  .hits | map({
    channel: $ch,
    id: .objectID,
    url: ("https://news.ycombinator.com/item?id=" + .objectID),
    title: (.story_title // ""),
    author: .author,
    body: (.comment_text // .story_text // ""),
    created_utc: .created_at,
    engagement: { comments: null, score: .points, reactions: null },
    raw_artifact_path: $artifact
  })
' "$HN_OUT" > "$SESSION_DIR/normalized/hn.json"
```

Filter to last `WINDOW_DAYS` via `created_at_i` (Unix timestamp): `select(.created_at_i > now - (WINDOW_DAYS*86400))`.

### 2.2 GitHub Issues Backend

**Why `gh api` (or curl when gh is unavailable):** GitHub's search API (`/search/issues`) exposes high-quality pain language - "this is broken", "I keep hitting", "anyone know a workaround". 30 req/min unauthenticated, 5000/h authenticated. We prefer `gh api` for auth-included rate (5000/h); fall back to anonymous `curl` if `gh` is unavailable (e.g. on a VPS without it).

```bash
# We search WITHOUT a repo:filter to capture cross-repo pain. We add `is:issue`
# to exclude PRs, and `in:body,comments` to catch pain in discussion text.
GH_OUT="$SESSION_DIR/raw/gh-${KEYWORD_HASH}.json"
QUERY="$KEYWORD is:issue in:body,comments created:>$(date -u -v-${WINDOW_DAYS}d +%Y-%m-%d 2>/dev/null || date -u -d "${WINDOW_DAYS} days ago" +%Y-%m-%d)"

if command -v gh >/dev/null 2>&1; then
  gh api "search/issues?q=$(jq -nr --arg q "$QUERY" '$q|@uri')&per_page=20" > "$GH_OUT"
else
  curl -s "https://api.github.com/search/issues?q=$(jq -nr --arg q "$QUERY" '$q|@uri')&per_page=20" \
    -H "Accept: application/vnd.github+json" > "$GH_OUT"
fi
sleep 2  # respect anonymous rate limits

# Normalize:
jq --arg ch "github" --arg artifact "$GH_OUT" '
  .items // [] | map({
    channel: $ch,
    id: (.html_url | split("/")[-1]),
    url: .html_url,
    title: .title,
    author: .user.login,
    body: (.body // ""),
    created_utc: .created_at,
    engagement: { comments: .comments, score: .reactions.total_count, reactions: .reactions.total_count },
    raw_artifact_path: $artifact
  })
' "$GH_OUT" > "$SESSION_DIR/normalized/github.json"
```

### 2.3 Reddit Backend (SearXNG proxy)

**Why SearXNG, not Reddit JSON:** Reddit's robots.txt is `User-agent: * Disallow: /`, our API app was rejected. SearXNG aggregates Google/Bing/DDG indexes - the same path Google itself uses, fully authorized. Localhost SearXNG on the VPS at `:8888`. For local Claude Code without VPS access, fall back to `WebFetch` against Google `site:reddit.com` queries (same pattern, slower).

```bash
# Reddit direct API blocked by robots.txt; route through SearXNG or Google site: search instead.
# Time range maps: day | week | month | year
WINDOW_RANGE="week"; [ "$WINDOW_DAYS" -gt 14 ] && WINDOW_RANGE="month"
QUERY=$(jq -nr --arg k "$KEYWORD" '"site:reddit.com \"" + $k + "\""' | jq -sRr @uri)

REDDIT_OUT="$SESSION_DIR/raw/reddit-${KEYWORD_HASH}.json"

# Local Claude Code: SearXNG might not be reachable; fall back to WebFetch on Google.
if curl -sf -m 3 "http://localhost:8888/" > /dev/null 2>&1; then
  curl -s "http://localhost:8888/search?q=${QUERY}&format=json&time_range=${WINDOW_RANGE}" \
    > "$REDDIT_OUT"
else
  echo "SearXNG not reachable at \$SEARXNG_URL - Reddit backend skipped (configure SEARXNG_URL env var or use WebFetch + Google site: search fallback)"
  echo '{"results":[]}' > "$REDDIT_OUT"
fi
sleep 2

# Filter to thread URLs (reddit.com/r/<sub>/comments/<id>/) and normalize:
jq --arg ch "reddit" --arg artifact "$REDDIT_OUT" '
  (.results // [])
  | map(select(.url | test("reddit\\.com/r/[^/]+/comments/")))
  | map({
      channel: $ch,
      id: (.url | capture("comments/(?<id>[a-z0-9]+)/").id),
      url: .url,
      title: .title,
      author: null,           # SearXNG result doesnt expose OP; would need second fetch
      body: (.content // ""),  # snippet is a partial body proxy
      created_utc: (.publishedDate // null),
      engagement: { comments: null, score: null, reactions: null },
      raw_artifact_path: $artifact
    })
' "$REDDIT_OUT" > "$SESSION_DIR/normalized/reddit.json"
```

### 2.4 Indie Hackers Backend (SearXNG site: query)

**Why not the IH page directly:** IH renders client-side via Ember.js. Static HTML returns the shell only. Routing through SearXNG `site:indiehackers.com` queries gets us indexed page snippets without browser automation. (The IH page exposes an Algolia search key in its config; we deliberately DO NOT use it - undocumented 3rd-party keys are a ToS risk and an inevitable break-when-they-rotate risk.)

```bash
# IH is SPA; SearXNG against the public Google/Bing index is the no-auth path.
QUERY=$(jq -nr --arg k "$KEYWORD" '"site:indiehackers.com \"" + $k + "\""' | jq -sRr @uri)

IH_OUT="$SESSION_DIR/raw/ih-${KEYWORD_HASH}.json"

if curl -sf -m 3 "http://localhost:8888/" > /dev/null 2>&1; then
  curl -s "http://localhost:8888/search?q=${QUERY}&format=json&time_range=${WINDOW_RANGE}" \
    > "$IH_OUT"
else
  echo "SearXNG not reachable - Indie Hackers backend skipped"
  echo '{"results":[]}' > "$IH_OUT"
fi
sleep 2

jq --arg ch "indiehackers" --arg artifact "$IH_OUT" '
  (.results // [])
  | map(select(.url | test("indiehackers\\.com/post/")))
  | map({
      channel: $ch,
      id: (.url | capture("post/(?<id>[A-Za-z0-9_-]+)").id),
      url: .url,
      title: .title,
      author: null,
      body: (.content // ""),
      created_utc: (.publishedDate // null),
      engagement: { comments: null, score: null, reactions: null },
      raw_artifact_path: $artifact
    })
' "$IH_OUT" > "$SESSION_DIR/normalized/indiehackers.json"
```

### 2.5 Merge

```bash
jq -s 'add' \
  "$SESSION_DIR/normalized/hn.json" \
  "$SESSION_DIR/normalized/github.json" \
  "$SESSION_DIR/normalized/reddit.json" \
  "$SESSION_DIR/normalized/indiehackers.json" \
  > "$SESSION_DIR/all-signals.json"
```

---

## PHASE 3: UNIFIED SCORING

One rubric, every channel. Score is 0-100 plus negative penalties.

| Component | Points | Source |
|---|---|---|
| **Pain severity (max 30)** | | |
| Title contains a high-pain keyword | +10 | regex on title |
| Body contains explicit pain language ("can't", "broken", "drowning", "killing me", "frustrated") | +10 | regex on body |
| Body asks an explicit question ("does anyone", "how do you", "is there a tool") | +10 | regex on body |
| **ICP fit (max 30)** | | |
| Author title or bio matches Tier A title (when channel exposes it) | +15 | author lookup |
| Body mentions an ICP-relevant tool/stack | +10 | tech keyword match |
| Body mentions budget signal ("willing to pay", "subscribed to", "happy to pay") | +5 | regex |
| **Recency (max 20)** | | |
| Created in last 24h | +20 | created_utc |
| Created in last 7d | +15 | created_utc |
| Created in last 14d | +10 | created_utc |
| Created in last 30d | +5 | created_utc |
| **Engagement (max 20)** | | |
| 10+ comments / replies | +10 | engagement.comments |
| 25+ score / upvotes / reactions | +10 | engagement.score |
| **Negatives** | | |
| Body contains an ICP disqualifier (negative_icp match) | -25 | regex |
| Title is meme / shitpost / off-topic ("haha", "shower thought", "unpopular opinion") | -15 | regex |
| Author is a known competitor or vendor (negative author list) | -20 | author lookup |
| Already a long thread with answer accepted (GitHub `state:closed`, HN dead-thread) | -10 | channel-specific |

**Tiers:**
- **Tier 1 (draft now):** score >= 60
- **Tier 2 (monitor):** 30-59
- **Tier 3 (skip):** < 30

Compute scores in jq or a small bash loop; write to `$SESSION_DIR/scored.json` keeping all original fields plus `score`, `score_breakdown`, `tier`.

---

## PHASE 4: OUTPUTS + DRAFTS + VERIFY

### 4.1 Canonical output

`vault/leads/hunt/YYYY-MM-DD/signals.json` - the full scored set. Other skills (`/outreach`, `/copy`, `/social`) consume from here.

### 4.2 Human-readable top-N

`vault/leads/hunt/YYYY-MM-DD/top-signals.md`:

```markdown
# Hunt - YYYY-MM-DD

ICP: [digest]
Channels: [list]
Window: last [N] days

## Tier 1 ([N] signals)

### 1. [HN/GitHub/Reddit/IH] - "[title]" - score [N]
- URL: [url]
- Author: [username]
- Pain quote: "[verbatim 1-2 sentences from body]"
- ICP-fit notes: [why this matches]
- Suggested approach: [one line - "answer the question, mention X if directly relevant"]

### 2. ...

## Tier 2 ([N] signals - monitor)
[list with one-line summaries]

## Cross-channel themes
- [Recurring pain phrases observed across channels]
- [Recurring competitor mentions]
- [Suggested keywords for next run]
```

### 4.3 Per-signal value-first reply drafts (Tier 1 only)

For each Tier 1 signal, draft a public reply matching channel etiquette:

- **HN:** terse, technical, no emoji, link to a concrete resource
- **GitHub:** acknowledge the issue, suggest a workaround, mention the pattern (not the product)
- **Reddit:** match the sub's register, no "I work on" unless the sub allows disclosed self-promo
- **Indie Hackers:** founder-to-founder tone, share the lesson, soft mention if directly relevant

Draft template:
```
[Acknowledge the pain in 1 sentence - prove you read it]

[Provide the substantive answer in 2-4 sentences. The value is complete WITHOUT our product.]

[Optional single-line product mention IF directly relevant: "FWIW, if this is a recurring pattern, X automates this loop - no pressure, just fits the use case."]

[Close with a question or a follow-up invitation.]
```

Draft constraints:
- 50-200 words
- Never lead with the product
- Product mention count: 0 or 1 (never 2+)
- Links go to free/public resources, not pricing pages
- Match the channel's register

Save each draft to `vault/leads/hunt/YYYY-MM-DD/drafts/<channel>-<id>.md`.

### 4.4 Verify (Steel Principle #5 enforcement)

- [ ] Every signal in `signals.json` has a `raw_artifact_path` that exists on disk
- [ ] Every Tier 1 draft has a corresponding signal in `signals.json`
- [ ] Score breakdowns sum to the reported `score`
- [ ] No draft exceeds 200 words
- [ ] No draft mentions the product more than once
- [ ] Zero DMs produced (public-reply-only invariant)

If any check fails, mark the affected signal `status: invalid` in `signals.json` and exclude from the SITREP top-N.

---

## PHASE 5: SITREP + HANDOFF

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```markdown
## SITREP - /hunt

**Skill:** /hunt
**ICP:** [digest]
**Window:** last [N] days
**Channels swept:** [list]
**Status:** COMPLETE

### Findings Summary

| Tier | Count | Action |
|------|-------|--------|
| Tier 1 (>=60) | [N] | Drafts ready in vault/leads/hunt/[date]/drafts/ |
| Tier 2 (30-59)| [N] | Monitor; re-run weekly |
| Tier 3 (<30)  | [N] | Filtered |

### Per-Channel Yield

| Channel | Raw | After ICP filter | Tier 1 |
|---------|-----|-----|-----|
| HN          | [N] | [N] | [N] |
| GitHub      | [N] | [N] | [N] |
| Reddit      | [N] | [N] | [N] |
| Indie Hackers| [N] | [N] | [N] |

### Top 5 Signals

1. [channel] - "[title]" - score [N] - [url]
2. ...

### Cleanup

| Resource | Status |
|----------|--------|
| Cache (.hunt-cache/) | 24h TTL applied |
| Raw artifacts | Kept in session dir for verification |
| Drafts | Written, NEVER auto-posted |

### Next Steps

1. Review drafts in vault/leads/hunt/[date]/drafts/
2. Run `/outreach` (when built) to convert Tier 1 signals into personalized DM/email drafts
3. Run `/copy` to polish a top draft
4. Run `/social` to adapt cross-channel themes into LinkedIn/X content
```

---

## RELATED SKILLS

**Feeds from:** `/prospect` (icp.json), `/icp-from-repo` (indirect, via /prospect)

**Feeds into:** `/outreach` (per-signal personalized DMs - when built), `/copy` (polish a draft, adapt voice-of-customer language), `/social` (cross-channel themes into LinkedIn/X), `/campaign` (Tier 1 yield informs channel mix), `/marketing` (VoC language for messaging framework)

**Pairs with:** `/scrape` (deep-dive a high-score thread), `/narrative` (turn a Tier 1 signal into a story angle), `/research-deep` (same SearXNG backbone)

**Auto-suggest after completion:**
- "Want me to draft personalized outreach with /outreach for the top 3?"
- "Want to polish the top draft with /copy?"
- "Want to spin the cross-channel themes into a LinkedIn post via /social?"

---

## ARCHIVE

After delivering the SITREP, archive so `/search` can surface it forever (per Live Document Protocol - hunts are research artifacts):

```bash
# Optional archive hook. If a skill-epilogue helper is configured (e.g. on an
# agent host that indexes skill outputs into a second-brain or knowledge base),
# invoke it. Local Claude Code installs typically skip this block.
SKILL_EPILOGUE_SCRIPT="${SKILL_EPILOGUE_SCRIPT:-}"
if [ -n "$SKILL_EPILOGUE_SCRIPT" ] && [ -x "$SKILL_EPILOGUE_SCRIPT" ]; then
  TMP_OUT="$(mktemp)"
  printf '%s\n' "$SKILL_BODY" > "$TMP_OUT"
  SKILL_KIND=research \
  SKILL_TITLE="Hunt $(date -u +%Y-%m-%d)" \
  SKILL_OUTPUT_FILE="$TMP_OUT" \
  SKILL_TAGS="hunt,prospect,leads,pain-signal,voc" \
  SKILL_SOURCE="skill:hunt" \
    bash "$SKILL_EPILOGUE_SCRIPT"
  rm -f "$TMP_OUT"
fi
```

---

## ADDING NEW CHANNELS (v2 PLUG-IN GUIDE)

To add a channel (LinkedIn Pulse, Product Hunt, G2, Capterra, X, etc.) in v2:

1. Add a new sub-section under Phase 2: `### 2.N <channel> Backend (<auth method>)`
2. Implement: query → cache raw response → normalize to the plug-in contract shape
3. Add the channel name to the merge step in 2.5
4. Add channel-specific draft etiquette to Phase 4.3
5. Update the per-channel yield table in the SITREP

The scoring rubric in Phase 3 does NOT change. The plug-in contract is the scoring's API.
