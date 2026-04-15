---
description: "/icp-from-repo - Extract product context from a product repo (local path OR github.com URL) and pre-fill /prospect's ICP workshop. Reads the product via filesystem or GitHub Contents API (zero disk footprint), writes product-brief.json, hands off to /prospect."
allowed-tools: Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Bash(find *), Bash(wc *), Bash(gh *), Bash(jq *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /icp-from-repo - Product Context Extraction for ICP Workshops

**Steel Principle: No ICP workshop starts cold when the product repo already answers half the questions.** If the codebase names the features, the README names the audience, and the pricing page names the tiers, extract all of it before interrogating the user.

This skill reads a product's repository and writes a structured `product-brief.json` that `/prospect` consumes in its Phase 0 Socratic interview. Answers the questions the user would otherwise type in manually: what the product does, who it appears to target, what tech stack it relies on, what language markets it addresses, and what pricing signals exist.

**Dual input mode - no cloning, ever:**

- **Local mode** - input is a filesystem path. The skill reads files directly via Read/Glob/Grep. Used when you are sitting in the product directory (typical for Claude Code on a dev workstation).
- **Remote mode** - input is a `github.com/owner/repo` URL. The skill reads files via `gh api repos/{owner}/{repo}/contents/{path}` and `gh api repos/{owner}/{repo}/git/trees/{ref}?recursive=true`. Zero disk footprint, no clone required. Used when the skill runs on an agent that does not have the repo locally (typical for autonomous agents running on a remote host).

Both modes produce the identical `product-brief.json` schema; downstream skills (`/prospect`, `/reddit-hunt`) do not care which source was used.

> **When to use /icp-from-repo:**
> - You own or have access to the product's source code
> - You are about to run `/prospect` and want to skip the cold interview
> - You are refreshing an existing ICP and want to surface new features added since last workshop
> - You are building an ICP for a product you acquired or inherited
>
> **When NOT to use /icp-from-repo:**
> - You are prospecting FOR someone else's product (no repo access) - go straight to `/prospect`
> - You need a marketing plan, not an ICP - use `/marketing`
> - You need competitor analysis - use `/scrape` on their landing pages first

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

**Steel Principle #1:** The repo is a data source, not a verdict. Everything extracted is a hypothesis the user confirms or overrides. Never ship a product-brief that wasn't reviewed.

**Steel Principle #2:** Marketing copy lies. Features in the code are ground truth; features in the landing page are aspirational. Cross-check both and flag the delta.

**Steel Principle #3:** Absence of signal is signal. If pricing, target audience, or technical requirements are missing from the repo, flag them as gaps the user must fill before `/prospect` runs.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The README says who it's for" | READMEs aspire; code reveals | Cross-check feature set vs. README claims; flag mismatches |
| "Just grab the landing page" | Landing page = marketing narrative; not product truth | Read landing page AND package.json AND actual UI components |
| "The user can fix inaccuracies later" | Inaccurate product-brief corrupts the entire ICP downstream | Flag every uncertain extraction; force user confirmation |
| "Pricing isn't in the repo, skip it" | Missing pricing is a buying-committee signal (self-serve vs enterprise) | Note the absence; ask the user during handoff |
| "I know the product, I don't need to re-read it" | Products drift; your knowledge goes stale | Re-scan every run; diff against previous product-brief if one exists |
| "Just clone the repo, it's easier" | Full clones waste 99%+ on node_modules/artifacts; they persist on disk and need cleanup; they fail for large monorepos | Use `gh api` for remote mode. Never `git clone`. If the agent really needs a clone for other reasons, that's a separate user action outside this skill |
| "The rate limit is fine, skip the check" | A 403 mid-run corrupts the session and produces a partial brief | Always check `gh api rate_limit` first; abort cleanly if under 100 remaining |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

Skill-specific cleanup:
- Extraction session folders live in gitignored `.icp-from-repo-reports/`
- The deliverable `.marketing-context/product-brief.json` IS a persistent artifact (tracked per project `.gitignore` conventions; default gitignored)
- No temporary files or sessions persist beyond the run

---

## WHEN TO USE /icp-from-repo vs. /prospect DIRECTLY

| Use /icp-from-repo first | Use /prospect directly |
|--------------------------|------------------------|
| Product repo is on disk | Prospecting for a product you cannot see |
| You want to skip cold-interview tedium | You want maximum founder/operator input |
| Multi-product company running batched ICP refreshes | One-off ICP for a single offering |
| ICP hasn't been refreshed in 6+ months | First ICP ever, deep Socratic is worth the time |

If you run `/icp-from-repo`, the skill ends with an explicit handoff: "Ready to run `/prospect`? Say yes and I will chain."

---

## SESSION FOLDER STRUCTURE

```
.icp-from-repo-reports/YYYYMMDD-HHMMSS-<product-slug>/
  interview-answers.md       (Phase 0 answers)
  scan-plan.md               (Phase 2 plan)
  raw-extractions/
    package-manifest.json    (normalized from package.json/Cargo.toml/go.mod/etc.)
    readme-summary.md
    landing-copy.md          (if detected)
    features-inventory.md    (from routes/components/API surfaces)
    pricing-signals.md
    stack-inventory.md
  gaps-and-questions.md      (Phase 4)
  sitrep.md
```

Persistent output (for `/prospect` to consume):
```
.marketing-context/product-brief.json
```

`.marketing-context/` is gitignored by convention in this skill library. Add it to `.gitignore` during Phase 1 setup:
```bash
grep -q ".marketing-context" .gitignore 2>/dev/null || echo ".marketing-context/" >> .gitignore
grep -q ".icp-from-repo-reports" .gitignore 2>/dev/null || echo ".icp-from-repo-reports/" >> .gitignore
```

---

## PHASE 0: SOCRATIC INTERVIEW

Ask these five questions. Wait for answers before scanning.

**Q1:** "Where is the repo? Give me ONE of: (a) a filesystem path like `./` or `/path/to/product`, or (b) a GitHub URL like `https://github.com/owner/repo`. Default is the current working directory."

Based on the answer, set the input mode:
- Path detected -> **local mode** (filesystem reads)
- `github.com/...` URL detected -> **remote mode** (`gh api` reads, zero disk footprint)
- Ambiguous -> ask to clarify

For remote mode, also verify `gh auth status` is OK and the repo is accessible (public, or user has a PAT with access). If access fails, surface the exact `gh` error and stop.

**Q2:** "Is this a single product, or a monorepo with multiple products? If multi-product, which one am I profiling? (In remote mode, a subdirectory path works: `owner/repo/apps/{{product_slug}}`.)"

**Q3:** "What is the product known by publicly? (Name as it appears on the website, app store, or marketing materials. Internal codenames don't help.)"

**Q4:** "Who do you think this product is for, in one sentence? (Your hypothesis. I'll cross-check it against the code and tell you where the evidence agrees or disagrees.)"

**Q5:** "Is there an existing `product-brief.json` I should refresh, or are we starting clean?"

Display a summary of answers (including the detected input mode) and confirm before Phase 1.

---

## PHASE 1: CONTEXT LOAD

**Setup:**
```bash
mkdir -p .marketing-context
mkdir -p .icp-from-repo-reports
grep -q ".marketing-context" .gitignore 2>/dev/null || echo ".marketing-context/" >> .gitignore
grep -q ".icp-from-repo-reports" .gitignore 2>/dev/null || echo ".icp-from-repo-reports/" >> .gitignore
```

**Repo structure scan (non-destructive, read-only). Behavior depends on input mode:**

### Local Mode (filesystem path)

Use Glob / Grep / Read directly:

- Root-level manifests: `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`, `composer.json`, `pom.xml`, `build.gradle`
- Documentation: `README.md`, `README.mdx`, `docs/`, `CHANGELOG.md`, `AGENTS.md`, `CLAUDE.md`
- Landing / marketing: `src/app/page.tsx`, `src/app/(marketing)/**`, `app/page.tsx`, `pages/index.tsx`, `public/index.html`, `content/`
- Product surface: `src/app/**`, `src/components/**`, `src/pages/**`, `app/**`, `api/**`, `routes/**`
- Pricing: Grep for `pricing`, `plans`, `tiers`, `stripe`, `subscription` across repo
- Feature flags or env: `.env.example`, `config/`, feature flag files

### Remote Mode (GitHub URL)

Use the GitHub Contents API via `gh` CLI. Zero disk footprint. Two-step pattern: list the tree, then read only the interesting files.

**Step 1 - list the tree (one call):**

```bash
OWNER=<from URL>
REPO=<from URL>
REF=$(gh api repos/${OWNER}/${REPO} --jq .default_branch)
gh api "repos/${OWNER}/${REPO}/git/trees/${REF}?recursive=true" \
  > .icp-from-repo-reports/<session>/tree.json
```

**Step 2 - filter to interesting paths (in-memory, no I/O):**

```bash
jq -r '.tree[] | select(.type=="blob") | .path' \
  .icp-from-repo-reports/<session>/tree.json \
  | grep -E '^(package\.json|pyproject\.toml|Cargo\.toml|go\.mod|Gemfile|composer\.json|README\.md|README\.mdx|CHANGELOG\.md|AGENTS\.md|CLAUDE\.md|\.env\.example|src/app/page\.(tsx|jsx)|src/app/\(marketing\)/.*|app/page\.(tsx|jsx)|pages/index\.(tsx|jsx)|public/index\.html|.*pricing.*\.(tsx|jsx|md|mdx))$' \
  > .icp-from-repo-reports/<session>/interesting-paths.txt
```

Also include up to 50 additional paths matching route patterns (`src/app/**/page.tsx`, `src/app/**/route.ts`, `src/pages/**/*.tsx` excluding `_*`, `app/routes/**/*.tsx` for Remix, OpenAPI specs) for the feature inventory. Cap total reads at 75 files per repo to stay well under the 5000 req/hr GitHub rate limit.

**Step 3 - read each interesting file via the Contents API:**

```bash
while IFS= read -r PATH; do
  gh api "repos/${OWNER}/${REPO}/contents/${PATH}" --jq '.content' \
    | base64 -d \
    > ".icp-from-repo-reports/<session>/raw-content/$(echo "$PATH" | tr '/' '__').txt"
  sleep 0.2  # Respect rate limit; 5 req/sec leaves headroom
done < .icp-from-repo-reports/<session>/interesting-paths.txt
```

**Rate limit guard:**

Before starting, check remaining quota and abort if insufficient:

```bash
REMAINING=$(gh api rate_limit --jq .resources.core.remaining)
if [ "$REMAINING" -lt 100 ]; then
  echo "GitHub API rate limit low ($REMAINING remaining). Aborting to avoid 403s."
  exit 1
fi
```

**Private repo access:** If the repo is private, `gh` uses the user's authenticated session (`gh auth status` must be OK) or the `GITHUB_TOKEN` / `GH_TOKEN` env var. No additional setup required.

**Never clone.** The skill does not shell out to `git clone` under any circumstances. If the user explicitly requests a clone for other reasons, stop and suggest they run `gh repo clone` themselves outside this skill.

### Both Modes: Optional Live URL Fetch

Ask: "Want me to also fetch the live product URL if the README links to one? This catches marketing copy that may differ from the repo. (Adds 2-5 min; skips if no URL.)"

If accepted and a URL is found in README or package.json homepage field, use WebFetch to grab the landing page HTML and extract headlines, sub-headlines, feature grid, and CTAs. This is independent of input mode - works the same whether the repo scan was local or remote.

---

## PHASE 2: PLAN + HARD GATE

Write `scan-plan.md` with:

- **Input mode:** local (filesystem) / remote (GitHub Contents API)
- **Source:** path or `owner/repo@ref`
- **Repo type detected:** (monorepo / single-product / app+docs split / other)
- **Languages + frameworks:** (from manifests)
- **Marketing pages found:** list of file paths
- **API/feature surface:** route count, component count, API endpoint count
- **Pricing signals:** found / partial / missing
- **Extraction strategy:** what to read in what order
- **API rate budget (remote mode only):** `{{N}}` of 5000 req/hr, `{{M}}` remaining pre-scan
- **Expected gaps:** (e.g., "no pricing page detected - user must supply tier structure")

**HARD GATE:**

```
═══════════════════════════════════════════════════════════════
SCAN PLAN REVIEW - /icp-from-repo
═══════════════════════════════════════════════════════════════

  Product:         {{product_name}}
  Input mode:      [local / remote]
  Source:          [/path/to/repo] OR [owner/repo@branch]
  Repo type:       [single / monorepo]
  Files to read:   [N source files, N docs, N marketing pages]
  API budget:      [N calls, M remaining]   (remote mode only)
  Live URL fetch:  [yes / no]
  Expected gaps:   [list]

  Proceed? Approve, or tell me what to change.
═══════════════════════════════════════════════════════════════
```

Do not proceed until the user approves.

---

## PHASE 3: EXECUTE

**Reading convention for this phase:**

- **Local mode** - read files directly via Read/Glob/Grep at their filesystem paths.
- **Remote mode** - files were already fetched to `.icp-from-repo-reports/<session>/raw-content/` in Phase 1 Step 3. Read from there; do NOT make additional `gh api` calls for files already retrieved. If a new file needs to be read mid-phase, append it to `interesting-paths.txt` and re-run Step 3 for that single path only (respecting the rate budget).

The extraction subsections below describe WHAT to extract; they apply identically to both modes.

### 3.1 Manifest Normalization

Read the primary package manifest and normalize to `raw-extractions/package-manifest.json`:

```json
{
  "name": "",
  "version": "",
  "description": "",
  "homepage": "",
  "repository": "",
  "keywords": [],
  "dependencies_core": [],
  "dev_dependencies_core": [],
  "framework_detected": "",
  "language": "",
  "license": ""
}
```

Core dependencies = top 10 by depth (production deps, not transitive dev noise).

### 3.2 README Synthesis

Read `README.md`. Extract:
- **Elevator pitch:** first non-badge paragraph
- **Feature bullets:** any bulleted list under "Features" or similar
- **Target audience hints:** grep for "built for", "designed for", "perfect for", "who this is for"
- **Installation surface:** one-line install / multi-step / enterprise-deploy (signals self-serve vs. high-touch)
- **Pricing / licensing:** free / OSS / paid-tier / mixed
- **Contact / sales channels:** email, Discord, Slack community, sales form URL

Write to `raw-extractions/readme-summary.md`.

### 3.3 Feature Inventory (Code-Ground-Truth)

Scan app routes and components to build the features inventory:

- **Next.js (app router):** list all `src/app/**/page.{tsx,jsx}` files; extract route paths
- **Next.js (pages router):** list all `pages/**/*.{tsx,jsx}` excluding `_*.tsx`
- **Remix:** `app/routes/**/*.tsx`
- **Django / Rails / Express:** route registration files
- **Generic:** any file named `*routes*`, `*urls*`, OpenAPI / Swagger specs

For each route/feature, try to infer user-visible label from:
- Component name
- Page title / metadata
- H1 or hero copy in the component

Write to `raw-extractions/features-inventory.md` as a bulleted list of user-visible features.

**Cross-check:** Compare README feature list vs. code feature list. Flag:
- **README claims, code absent:** aspirational marketing, clarify
- **Code has, README missing:** under-marketed feature, surface it

### 3.4 Landing Copy Extraction

If marketing pages were identified in Phase 1:
- Read each page's JSX/HTML
- Extract: H1, sub-headline, feature grid titles, social proof (testimonials, logos), CTAs, pricing copy

Write to `raw-extractions/landing-copy.md`.

### 3.5 Pricing Signal Extraction

Grep for pricing evidence across the repo:
- `stripe`, `lemonsqueezy`, `paddle` in deps or env = paid product
- A `pricing/page.tsx` or similar = explicit tiers present; extract tier names + price points + feature gates
- Env vars with `PRICE_ID_*` patterns = Stripe products defined
- No payment integration found = likely free, OSS, or pre-revenue

Write to `raw-extractions/pricing-signals.md`.

### 3.6 Tech Stack Inventory

From manifest + code sample:
- **Frontend:** framework + UI lib + state mgmt
- **Backend:** language + framework + DB
- **Infra hints:** Docker, Vercel, AWS SDK, K8s
- **AI/ML:** any `openai`, `anthropic`, `langchain`, local model runners

Write to `raw-extractions/stack-inventory.md`. This is a **negative-ICP input**: people who won't adopt because their stack is incompatible.

### 3.7 Product Brief Synthesis

Combine all extractions into `.marketing-context/product-brief.json`:

```json
{
  "version": "1.0",
  "generated_by": "/icp-from-repo",
  "generated_at": "YYYY-MM-DD",
  "product": {
    "name": "",
    "tagline": "",
    "elevator_pitch": "",
    "homepage_url": "",
    "repository_url": "",
    "license": ""
  },
  "features": {
    "marketed": [],
    "shipped_in_code": [],
    "delta": {
      "aspirational_not_yet_built": [],
      "shipped_but_under_marketed": []
    }
  },
  "audience_hypothesis": {
    "from_readme": "",
    "from_landing_copy": "",
    "from_feature_set": "",
    "user_supplied": ""
  },
  "stack": {
    "frontend": [],
    "backend": [],
    "infrastructure": [],
    "ai_ml": []
  },
  "pricing": {
    "model": "free | freemium | paid | enterprise | unknown",
    "tiers": [],
    "self_serve": "yes | no | partial | unknown",
    "payment_provider": ""
  },
  "buying_motion_hints": {
    "install_surface": "one-line | multi-step | enterprise-deploy",
    "contact_channels": [],
    "target_budget_tier": "individual | smb | midmarket | enterprise | unknown"
  },
  "gaps_requiring_user_input": []
}
```

### 3.8 Gaps + Questions File

Write `gaps-and-questions.md` listing everything the repo couldn't answer:

```markdown
## Gaps for User to Fill

1. **[Gap]:** Why it matters
   - Recommended source: [where to look]
   - Question to answer before running /prospect: [exact question]
```

Minimum gaps to always surface if not found:
- Named competitors (repo rarely has this)
- Customer count / revenue range
- Known trigger events that correlate with customer acquisition
- Anti-ICP examples from historical churn
- Geographic focus if not in landing copy

---

## PHASE 4: VERIFY

Before handing off to `/prospect`:

1. **Read the generated `product-brief.json` out loud to the user** (summarized):
   - "Product: [name]. Tagline: [X]. You said it's for [Y]; the code suggests [Z]. Marketing mentions [A, B, C]; code ships [A, B, D]. Feature [C] is aspirational or missing. Feature [D] is shipped but not marketed. Pricing: [model]. Stack: [summary]. Gaps: [N items]."
2. **Ask for corrections:** "Any of that wrong? Anything to add?"
3. **Persist corrections** to `product-brief.json` before handoff.

---

## PHASE 5: SITREP + HANDOFF

Reference `~/.claude/standards/SITREP_FORMAT.md`.

Domain-specific SITREP additions:
- **Product brief path:** `.marketing-context/product-brief.json`
- **Features marketed vs. shipped delta:** `{{N_aspirational}} aspirational, {{N_under_marketed}} under-marketed`
- **Gaps surfaced:** `{{N}} items requiring user input before /prospect runs`
- **Recommended next skill:** `/prospect` (with the product-brief as context)

Close with:
```
Handoff ready. Product brief saved.

→ Run /prospect to build the ICP on top of this brief?
→ Or /marketing if you need full campaign + positioning first?
```

---

## SELF-IMPROVEMENT HOOKS

Telemetry to log (append to `.icp-from-repo-reports/telemetry.jsonl`):
- `duration_seconds`
- `files_read`
- `features_marketed_vs_shipped_delta`
- `gaps_surfaced_count`
- `user_corrections_count` (how many times user overrode an extracted field)
- `handoff_to_prospect` (true if user chained)

If `user_corrections_count` > 3 consistently, the extraction logic needs refinement; flag in the SITREP.

---

## RELATED SKILLS

**Feeds from:** `/scrape` (if a competitor landing page was scraped first for contrast)

**Feeds into:** `/prospect` (primary consumer; pre-fills Phase 0 Socratic answers), `/marketing` (product-brief.json is a strategy input), `/copy` (elevator pitch + feature list as source material)

**Pairs with:** `/prospect` (chain together: `/icp-from-repo` then `/prospect`)

**Auto-suggest after completion:** "Product brief written. Run `/prospect` to turn this into a full ICP?"

---

## PATTERN

Pattern A (Socratic + Plan + Gate + Verify + SITREP) per [Skill Pattern Standard](~/.claude/standards/SKILL_PATTERN.md).
