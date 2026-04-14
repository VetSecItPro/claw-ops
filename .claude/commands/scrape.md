---
description: /scrape — Web scraping with Crawl4AI (self-hosted) or Jina Reader (fallback). Extract markdown, JSON, or structured data from any URL. Supports single-page, multi-page crawls, and full site maps.
allowed-tools: Bash(curl *), Bash(mkdir *), Bash(cat *), Bash(date *), Bash(docker *), Read, Write, Edit, Glob, Grep, Task, WebFetch, WebSearch
---

# /scrape — Web Scraping Pipeline

**Fast, clean, content-focused scraping.** Extract markdown, structured data, or sitemaps from any public URL using Crawl4AI (self-hosted, preferred) or Jina Reader (cloud fallback). Strips template garbage and returns actual content.

**FIRE AND FORGET** — Provide a URL and a goal. The skill detects the best backend, presents a plan, gets approval, executes, and delivers clean output.

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** No scrape without an explicit allowance check (robots.txt read, terms reviewed for commercial use). Sites you cannot scrape ethically get flagged and skipped.
- **Steel Principle #2:** Output is always cleaned content, never raw HTML unless explicitly requested. Template noise (nav, footer, cookie banners) gets stripped before save.
- **Steel Principle #3:** Cache first, then fetch. Do not re-hit the same URL within 24 hours unless explicitly refreshed.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "Robots.txt is just a suggestion" | Robots.txt is a legally-significant signal; ignoring it risks IP bans and potential legal exposure | Read robots.txt; respect crawl-delay and disallowed paths |
| "Just grab everything, filter later" | Oversized scrapes waste tokens and return template noise | Scope to the exact selector or section; iterate if needed |
| "Docker is down, use Jina" | Jina and Crawl4AI return different quality levels; silently swapping masks issues | Explicit fallback with a reason logged; user chooses to proceed or fix Crawl4AI |
| "I scraped this yesterday, re-scrape to be safe" | Wasted requests hurt target sites and cost tokens | Use the cache; force-refresh only when explicitly requested |

---

## WHEN TO USE /scrape vs. /browse

| Use /scrape | Use /browse |
|-------------|-------------|
| Static or server-rendered pages | JS-heavy SPAs where content needs user interaction |
| Batch multi-URL extraction | Single interactive sessions with clicking/typing |
| Scheduled / repeated scraping | Auth-gated pages (login required) |
| Markdown, JSON, structured output | Visual inspection, screenshots, CSS audit |
| Competitor or brand research | Dev server testing |

If the user needs to log in or interact before seeing content, stop and suggest `/browse` instead.

---

## SESSION FOLDER STRUCTURE

Every run creates a timestamped session folder:

```
.scrape-reports/YYYYMMDD-HHMMSS-<slug>/
  interview-answers.md      (Phase 0 answers)
  plan.md                   (live-updated plan)
  scraped/
    <url-slug>.md           (one per URL, markdown output)
    <url-slug>.json         (structured data if requested)
  metadata.json             (URLs, status codes, response times, backend used)
  verification.md           (Phase 4 checks)
  sitrep.md                 (final SITREP)
```

`.scrape-reports/` MUST be in `.gitignore`. Setup adds it automatically:
```bash
grep -q ".scrape-reports" .gitignore 2>/dev/null || echo ".scrape-reports/" >> .gitignore
```

---

## CACHING

Use `.scrape-cache/<url-hash>.md` to avoid redundant re-scrapes. Check file mtime before requesting:

```bash
# Cache hit check (24h default, configurable via SCRAPE_CACHE_HOURS)
CACHE_DIR=".scrape-cache"
URL_HASH=$(echo -n "$URL" | md5sum | cut -c1-16)
CACHE_FILE="$CACHE_DIR/${URL_HASH}.md"
MAX_AGE="${SCRAPE_CACHE_HOURS:-24}"

if [ -f "$CACHE_FILE" ]; then
  AGE_HOURS=$(( ($(date +%s) - $(date -r "$CACHE_FILE" +%s)) / 3600 ))
  if [ "$AGE_HOURS" -lt "$MAX_AGE" ]; then
    echo "Cache hit: $CACHE_FILE (${AGE_HOURS}h old)"
    # Use cached content
  fi
fi
```

Pass `--fresh` in the user's request to bypass cache. Always report whether output is cached or fresh.

---

## ENVIRONMENT VARIABLES

| Variable | Default | Purpose |
|----------|---------|---------|
| `CRAWL4AI_URL` | `http://localhost:11235` | Override Crawl4AI server URL |
| `CRAWL4AI_AUTH_TOKEN` | (none) | Bearer token if server requires auth |
| `SCRAPE_CACHE_HOURS` | `24` | Cache TTL in hours; set to `0` to disable |
| `JINA_API_KEY` | (none) | Optional: higher rate limits on Jina fallback |

---

## MCP INTEGRATION

Crawl4AI exposes an MCP server when running. Configure it in `~/.claude/mcp.json` to use native MCP tools instead of curl:

```json
{
  "mcpServers": {
    "crawl4ai": {
      "url": "http://localhost:11235/mcp"
    }
  }
}
```

When the MCP server is configured, Crawl4AI tools will appear as native tools in Claude Code. The curl patterns in this skill work regardless - MCP is an optional enhancement, not a requirement.

---

## ROBOTS.TXT COMPLIANCE

Before scraping any domain, fetch `/robots.txt`:

```bash
curl -s "https://example.com/robots.txt" 2>/dev/null
```

Honor these directives:
- `Disallow:` rules for the paths being requested
- `Crawl-delay:` set request pacing accordingly (default: 1 req/sec)
- `User-agent:` check for Crawl4AI or generic bot rules

If a path is Disallowed, skip it, report it in verification.md, and move on. Do not attempt to bypass robots.txt rules.

---

## ANTI-SLOP FOR SCRAPED CONTENT

After scraping, strip the following before saving output:

- Cookie consent banners and GDPR notices
- "Subscribe to newsletter" and email capture CTAs
- Navigation menus and breadcrumbs (unless specifically requested)
- Footer boilerplate (links, copyright, social icons)
- "You might also like" / "Related posts" sidebar blocks
- Chat widget placeholder text
- Generic meta-section filler ("Last updated: ...", "Reading time: N min")

Keep: hero content, body copy, headings, feature lists, pricing data, testimonials, and any content that answers the user's stated goal.

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Reports and artifacts live in gitignored `.scrape-reports/` directories
- Any temporary files or sessions are closed at the end of the run
- No persistent resources are left behind unless explicitly requested

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these 5 questions before doing anything. One at a time in conversation, but list them all upfront so the user can answer in one message:

1. **Target scope:** Single URL, a list of URLs, or crawl an entire site via sitemap?
2. **Output format:** Markdown content (for reading/analysis), structured fields (JSON extraction), or metadata only?
3. **Site type:** Is this a JS-heavy SPA (React/Vue/Angular) or a server-rendered/static site?
4. **Reuse:** One-time scrape or something you'll run again? (Determines whether to set up caching.)
5. **Auth:** Does accessing any target URL require logging in first? (If yes, stop here - use `/browse` instead.)

Save answers to `$SESSION_DIR/interview-answers.md`.

---

## PHASE 1: BACKEND DETECTION

Check availability in this priority order:

### 1. Local Crawl4AI (preferred)

```bash
curl -s --max-time 3 http://localhost:11235/health
```

Healthy response: `{"status": "ok"}` or similar. If healthy, use this. Fastest, no rate limits, unlimited usage.

### 2. Remote Crawl4AI via Tailscale (fallback if local unavailable)

Check `CRAWL4AI_URL` env var first. If not set, try known Tailscale IPs:

```bash
# ironworks-vps
curl -s --max-time 3 http://100.93.111.58:11235/health

# claw-hq
curl -s --max-time 3 http://100.90.180.107:11235/health
```

Use the first one that responds healthy.

### 3. Jina Reader (always-available fallback)

If no Crawl4AI instance is reachable, fall back to Jina Reader. Free tier, no server required, handles most public pages well. Rate limit: ~10 req/min on free tier.

```bash
# With optional API key for higher limits
JINA_HEADER=""
if [ -n "$JINA_API_KEY" ]; then
  JINA_HEADER="-H 'Authorization: Bearer $JINA_API_KEY'"
fi
curl $JINA_HEADER -H "X-Return-Format: markdown" "https://r.jina.ai/https://example.com"
```

**Report to user:** "Using [Crawl4AI local / Crawl4AI remote at X / Jina Reader]. [Reason if not using preferred.]"

---

## PHASE 2: PLAN + HARD GATE

Write `$SESSION_DIR/plan.md` with:

```markdown
# Scrape Plan

**Date:** [timestamp]
**Backend:** [Crawl4AI local | Crawl4AI remote (IP) | Jina Reader]
**Session:** [session folder path]

## Target URLs

| # | URL | Cache Status | Notes |
|---|-----|-------------|-------|
| 1 | [url] | [fresh | cached Xh] | [any notes] |

## Output Format

[markdown | structured JSON with schema | metadata only]

## Schema (if structured extraction)

```json
{
  "field_name": "description of what to extract"
}
```

## Caching Strategy

TTL: [Xh | disabled]. Cache dir: .scrape-cache/

## Rate Limiting

[X req/sec. Robots.txt: [compliant | no rules found for target paths]]

## Estimated Time

[X URLs at Y req/sec = ~Z seconds]
```

Present the plan. **HARD GATE:**

```
═══════════════════════════════════════════════════════════════
SCRAPE PLAN REVIEW
═══════════════════════════════════════════════════════════════

  Backend:   [backend name]
  URLs:      [count] ([list if small, "see plan.md" if large])
  Output:    [format]
  Cache:     [strategy]
  Robots.txt: [compliant / not checked (provide domain)]

  Approved? Or revise?
═══════════════════════════════════════════════════════════════
```

Do not execute until the user approves.

---

## PHASE 3: EXECUTE

### Crawl4AI Patterns

**Single URL to markdown:**
```bash
curl -s -X POST "${CRAWL4AI_URL:-http://localhost:11235}/scrape" \
  -H "Content-Type: application/json" \
  ${CRAWL4AI_AUTH_TOKEN:+-H "Authorization: Bearer $CRAWL4AI_AUTH_TOKEN"} \
  -d '{
    "url": "https://example.com",
    "output_format": "markdown",
    "bypass_cache": false
  }'
```

**Multiple URLs (batch):**
```bash
curl -s -X POST "${CRAWL4AI_URL:-http://localhost:11235}/crawl" \
  -H "Content-Type: application/json" \
  ${CRAWL4AI_AUTH_TOKEN:+-H "Authorization: Bearer $CRAWL4AI_AUTH_TOKEN"} \
  -d '{
    "urls": ["https://example.com/page1", "https://example.com/page2"],
    "output_format": "markdown"
  }'
```

**Structured data extraction (JSON schema):**
```bash
curl -s -X POST "${CRAWL4AI_URL:-http://localhost:11235}/extract" \
  -H "Content-Type: application/json" \
  ${CRAWL4AI_AUTH_TOKEN:+-H "Authorization: Bearer $CRAWL4AI_AUTH_TOKEN"} \
  -d '{
    "url": "https://example.com/pricing",
    "schema": {
      "product_name": "Name of the product or plan",
      "price": "Price per month or year",
      "features": "List of included features"
    }
  }'
```

**Full site via sitemap:**
```bash
curl -s -X POST "${CRAWL4AI_URL:-http://localhost:11235}/crawl_sitemap" \
  -H "Content-Type: application/json" \
  ${CRAWL4AI_AUTH_TOKEN:+-H "Authorization: Bearer $CRAWL4AI_AUTH_TOKEN"} \
  -d '{
    "sitemap_url": "https://example.com/sitemap.xml",
    "output_format": "markdown",
    "max_pages": 50
  }'
```

### Jina Reader Patterns

```bash
# Markdown output (default)
curl -s -H "X-Return-Format: markdown" \
  ${JINA_API_KEY:+-H "Authorization: Bearer $JINA_API_KEY"} \
  "https://r.jina.ai/https://example.com"

# JSON output
curl -s -H "X-Return-Format: json" \
  ${JINA_API_KEY:+-H "Authorization: Bearer $JINA_API_KEY"} \
  "https://r.jina.ai/https://example.com"
```

### Saving Output

For each scraped URL, generate a slug and save:

```bash
# Convert URL to safe filename slug
URL_SLUG=$(echo "$URL" | sed 's|https\?://||' | sed 's|[^a-zA-Z0-9]|-|g' | sed 's|-\+|-|g' | sed 's|^-\|-$||g' | cut -c1-80)

# Save markdown
echo "$CONTENT" > "$SESSION_DIR/scraped/${URL_SLUG}.md"

# Save JSON if structured extraction
echo "$JSON_CONTENT" > "$SESSION_DIR/scraped/${URL_SLUG}.json"
```

Also update `$SESSION_DIR/metadata.json` after each URL:

```json
{
  "session": "YYYYMMDD-HHMMSS-slug",
  "backend": "crawl4ai-local",
  "urls": [
    {
      "url": "https://example.com",
      "slug": "example-com",
      "status": "success",
      "http_code": 200,
      "response_time_ms": 342,
      "content_length": 8420,
      "cached": false,
      "scraped_at": "2026-04-11T14:32:00Z"
    }
  ]
}
```

---

## PHASE 4: VERIFICATION

Check every URL in the plan:

- **Status check:** Did every URL succeed (no 404, 429, timeout, or empty body)?
- **Content check:** Is the markdown content meaningful (not just "Enable JavaScript to use this app")?
- **Truncation check:** Is the content suspiciously short (< 200 chars) for a page that should have real content?
- **Schema check (if structured extraction):** Are all requested schema fields populated, or are some null/empty?
- **Robots.txt:** Were any URLs skipped due to Disallow rules?

Write `$SESSION_DIR/verification.md`:

```markdown
# Verification Report

| URL | Status | Content Length | Schema Complete | Issues |
|-----|--------|---------------|----------------|--------|
| [url] | OK | 8420 chars | N/A | none |
| [url] | FAIL | 0 chars | N/A | Empty response - JS-rendered? |
```

For any failure, report the specific error and suggest a remedy (try Jina if Crawl4AI returned empty, use `/browse` if JS rendering is the problem).

---

## PHASE 5: SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

Write to `$SESSION_DIR/sitrep.md` AND display on screen:

```
═══════════════════════════════════════════════════════════════
SITREP — /scrape
═══════════════════════════════════════════════════════════════
Project:  [project or "standalone session"]
Branch:   [git branch or "N/A"]
Mode:     [single-url | multi-url | sitemap]
Started:  [timestamp]
Completed:[timestamp]
Status:   [COMPLETE / PARTIAL / BLOCKED]
───────────────────────────────────────────────────────────────

BACKEND
  Used: [Crawl4AI local | Crawl4AI remote (IP) | Jina Reader]
  Reason: [preferred available | fallback used because X]

SCOPE
  URLs requested: [N]
  URLs succeeded: [N]
  URLs failed:    [N]
  Robots.txt blocked: [N]

OUTPUT
  Format: [markdown | JSON | structured]
  Total content: [X KB]
  Cached (reused): [N]
  Fresh (fetched): [N]
  Session folder: [path]

FAILURES (if any)
  [url] — [specific error + suggested remedy]

CLEANUP
  Cache: [N] entries written to .scrape-cache/
  Session: [path] (kept — output files)

NEXT STEPS
  1. [immediate action]
  2. [follow-up]

SUGGESTED NEXT SKILLS
───────────────────────────────────────────────────────────────
  Condition                          | Run
  -----------------------------------|---------------------------
  Brand analysis from scraped pages  | /marketing (Module F)
  Competitor research complete       | /prospect
  Design reference pages scraped     | /design
  Blog source research done          | /blog
───────────────────────────────────────────────────────────────
Report: .scrape-reports/[session]/sitrep.md
═══════════════════════════════════════════════════════════════
```

---

## WORKED EXAMPLES

### Example 1: Brand Analysis Before Marketing Campaign

**Goal:** Scrape a competitor's site before running `/marketing` to understand their positioning.

**Interview answers:**
- Target: 5 URLs (homepage, /about, /pricing, /features, /customers)
- Output: Markdown for analysis
- Site type: Server-rendered Next.js marketing site
- Reuse: One-time
- Auth: None

**Phase 1:** Crawl4AI local available. Use it.

**Phase 3 execution:**
```bash
curl -s -X POST http://localhost:11235/crawl \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      "https://competitor.com",
      "https://competitor.com/about",
      "https://competitor.com/pricing",
      "https://competitor.com/features",
      "https://competitor.com/customers"
    ],
    "output_format": "markdown"
  }'
```

**Output:**
```
.scrape-reports/20260411-143200-competitor-com/
  scraped/
    competitor-com.md           (hero, positioning, social proof)
    competitor-com-about.md     (founding story, team, mission)
    competitor-com-pricing.md   (tiers, feature gates, CTA language)
    competitor-com-features.md  (feature list, differentiators)
    competitor-com-customers.md (customer logos, case study summaries)
```

**What to do next:** Feed these files into `/marketing` Module F for brand guide generation.

---

### Example 2: Competitor Pricing Research (Structured Extraction)

**Goal:** Extract structured pricing data from 3 competitors for a pricing comparison table.

**Interview answers:**
- Target: 3 pricing pages (one per competitor)
- Output: Structured JSON (plan name, price, features list)
- Site type: Mix - check each
- Reuse: Quarterly (set up cache)
- Auth: None

**Phase 3 execution:**
```bash
for URL in \
  "https://competitor-a.com/pricing" \
  "https://competitor-b.com/pricing" \
  "https://competitor-c.com/pricing"; do
  curl -s -X POST http://localhost:11235/extract \
    -H "Content-Type: application/json" \
    -d "{
      \"url\": \"$URL\",
      \"schema\": {
        \"plans\": \"List of pricing plan names\",
        \"prices\": \"Price per plan (monthly and annual if available)\",
        \"features\": \"Key features per plan\",
        \"free_tier\": \"Is there a free tier? What are its limits?\",
        \"trial\": \"Free trial available? How many days?\"
      }
    }"
  sleep 1  # 1 req/sec rate limiting
done
```

**Output:** 3 JSON files ready for comparison table generation.

**What to do next:** `/prospect` for ICP scoring against competitor gaps, or `/copy` to write positioning copy that exploits identified weaknesses.

---

### Example 3: Full Site Content Audit via Sitemap

**Goal:** Scrape all blog posts from a knowledge base to research a topic.

**Interview answers:**
- Target: Full site via sitemap (blog section only)
- Output: Markdown
- Site type: Static site (Hugo/Jekyll)
- Reuse: Monthly refresh (24h cache)
- Auth: None

**Phase 3 execution:**
```bash
# First: check sitemap structure
curl -s https://target-site.com/sitemap.xml | head -50

# Then: crawl blog section only (filter sitemap for /blog/ paths)
curl -s -X POST http://localhost:11235/crawl_sitemap \
  -H "Content-Type: application/json" \
  -d '{
    "sitemap_url": "https://target-site.com/blog/sitemap.xml",
    "output_format": "markdown",
    "max_pages": 100,
    "url_filter": "/blog/"
  }'
```

**Output:** Up to 100 markdown files, one per blog post. Metadata JSON includes all URLs, word counts, and scrape timestamps.

**What to do next:** `/blog` for long-form content production informed by the research corpus.

---

## RELATED SKILLS

**Feeds from:** (none - entry point)

**Feeds into:**
- `/marketing` (Module F) - brand analysis and brand guide generation from scraped pages
- `/prospect` - competitor research, ICP validation from competitor customer pages
- `/design` - design reference sites scraped for visual inspiration
- `/blog` - source research and corpus building for long-form articles
- `/compliance-docs` - policy and regulatory research from authoritative sources

**Pairs with:**
- `/browse` - when pages require authentication or interactivity before content is accessible; `/scrape` handles the public content, `/browse` handles the gated sessions

**Auto-suggest after completion:**
- If brand/competitor pages were scraped: suggest `/marketing` Module F (brand guide generation)
- If pricing pages were scraped: suggest `/prospect` for ICP and competitive gap analysis
- If blog/documentation scraped: suggest `/blog` with corpus as source material

---

## SETTING UP CRAWL4AI

### Option 1: Docker (local, recommended)

```bash
docker pull unclecode/crawl4ai:latest
docker run -d \
  --name crawl4ai \
  -p 11235:11235 \
  --restart unless-stopped \
  unclecode/crawl4ai:latest
```

Verify: `curl http://localhost:11235/health`

### Option 2: Docker Compose (production)

```yaml
services:
  crawl4ai:
    image: unclecode/crawl4ai:latest
    ports:
      - "11235:11235"
    restart: unless-stopped
    environment:
      - CRAWL4AI_API_TOKEN=${CRAWL4AI_AUTH_TOKEN:-}
    volumes:
      - crawl4ai_cache:/app/.crawl4ai

volumes:
  crawl4ai_cache:
```

### Option 3: pip (local dev)

```bash
pip install crawl4ai
crawl4ai-setup
python -m uvicorn crawl4ai.api:app --host 0.0.0.0 --port 11235
```

### Verify Installation

```bash
# Health check
curl http://localhost:11235/health

# Test scrape
curl -X POST http://localhost:11235/scrape \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com", "output_format": "markdown"}'
```

---

## REMEMBER

- **Check robots.txt first** - always honor Disallow rules and Crawl-delay before any scraping
- **Verify content, not just HTTP status** - a 200 response with "Enable JavaScript" is a failure
- **Strip template garbage** - navigation, footers, and banners are not content
- **Cache is your friend** - avoid re-scraping unchanged pages; 24h TTL is the right default
- **Jina is always available** - if all Crawl4AI instances are down, Jina Reader handles most public pages
- **Auth = /browse** - if the user mentions logging in, do not attempt to scrape; redirect to `/browse`
- **Rate limit by default** - 1 req/sec unless robots.txt specifies faster is acceptable

<!-- Claude Code Skill by Steel Motion LLC — https://steelmotion.dev -->
<!-- Part of the Claude Code Skills Collection -->
<!-- Powered by Claude models: Haiku (fast extraction), Sonnet (balanced reasoning), Opus (deep analysis) -->
<!-- License: MIT -->
