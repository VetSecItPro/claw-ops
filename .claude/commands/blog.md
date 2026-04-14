---
description: "/blog — Long-form article pipeline: angle → research → outline → draft → voice pass → anti-slop scrub → cut → fact-check → image → publish. Website + Substack modes. Voice-anchored to your existing articles, Fal.ai hero images, hard gate on AI slop."
allowed-tools: Bash(git *), Bash(npm *), Bash(npx *), Bash(pnpm *), Bash(yarn *), Bash(curl *), Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(wc *), Bash(head *), Bash(tail *), Bash(cp *), Bash(mv *), Bash(file *), Bash(du *), Bash(identify *), Bash(python3 *), Bash(jq *), Bash(date *), Bash(grep *), Bash(find *), Read, Write, Edit, Glob, Grep, Task, Skill, WebFetch, WebSearch
---

# /blog — Long-Form Article Pipeline

**Produce articles worth publishing.** Nine phases from angle to shipped post. Voice-anchored to existing articles in `articles/VOICE.md`. Anti-slop discipline enforced at Phase 6. Fal.ai Flux Pro for hero images. Website first, Substack second.

**FIRE AND FORGET after intake** - once the angle is approved, the skill runs through all phases, pausing only at explicit hard gates (angle approval, outline approval, cut draft approval).

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #1:** No article shipped without running the anti-slop scrub in Phase 6 against the full draft. Not a sample. The full draft.
- **Steel Principle #2:** No claim without a named source. "Experts say" is rejected. "Simon Willison, who created Datasette, wrote in October 2025..." is required.
- **Steel Principle #3:** Voice fingerprint load happens before any prose is written. No draft starts without `articles/VOICE.md` read this session.
- **Steel Principle #4:** Hard gates at Phase 1 (angle), Phase 3 (outline), and Phase 7 (pre-fact-check draft). No proceeding past a gate without explicit user approval.
- **Steel Principle #5:** Reader specificity is required. Before Phase 4, name ONE specific person this article is for. Generic readers produce generic articles.

### Blog-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The draft reads fine, skip the slop scrub" | Fine-reading drafts are the most insidious slop; you stop noticing after writing | Run the scrub. Every time. Without exception. |
| "This is a short post, voice doesn't matter" | Short posts that sound generic are worse than long posts that sound like you | Load VOICE.md and pass the fingerprint check regardless of length |
| "I know the topic, skip the research phase" | Confident knowledge produces unsourced claims that get corrected in comments | At minimum 3 named sources with dates and credentials |
| "The counterargument is obvious, skip it" | If it's obvious, it takes one paragraph to address and it lifts the whole piece | Name the disagreement explicitly when the article type calls for it |
| "Let me add an inspirational quote to close" | Closing quotes are the highest-density slop signal in AI writing | Close on your strongest claim, not someone else's words |
| "I'll generate the image after it publishes" | Images drive social engagement; hero image ships with the article or slips to never | Generate in Phase 9, review, approve, ship together |

---

## STATUS UPDATES

This skill follows the [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md). See standard for emoji format and cadence rules.

```
🧠 Phase 0: INTAKE
🎯 Phase 1: ANGLE (user-approval gate)
🔍 Phase 2: RESEARCH (N sources collected)
📋 Phase 3: OUTLINE (user-approval gate)
✍️  Phase 4: DRAFT LONG (target: 3x final length)
🎙️  Phase 5: VOICE PASS
🧹 Phase 6: ANTI-SLOP SCRUB (pattern checks)
✂️  Phase 7: CUT (target: 50% removed, user-approval gate)
✅ Phase 8: FACT PASS
🎨 Phase 9: IMAGE (Fal.ai Flux Pro)
🚀 Phase 10: PUBLISH (website → Substack handoff)
```

---

## CONTEXT MANAGEMENT

This skill follows the [Context Management Protocol](~/.claude/standards/CONTEXT_MANAGEMENT.md).

Large research sweeps dispatch sub-agents that return <500-token summaries. Full source content lives on disk in `articles/research/[slug]/`. The orchestrator reads summaries, never full source dumps.

---

## OUTPUT STRUCTURE

```
~/vibecode-projects/steelmotion/articles/
├── VOICE.md                          # Voice fingerprint (DO NOT regenerate per article)
├── drafts/
│   └── YYYY-MM-DD-[slug]/
│       ├── angle.md                  # Phase 1 output
│       ├── research.md               # Phase 2 aggregated output
│       ├── outline.md                # Phase 3 output
│       ├── draft-v1-long.md          # Phase 4 output (3x length)
│       ├── draft-v2-voice.md         # Phase 5 output
│       ├── draft-v3-scrubbed.md      # Phase 6 output
│       ├── draft-v4-cut.md           # Phase 7 output
│       ├── draft-v5-fact-checked.md  # Phase 8 output (= final)
│       └── slop-scrub-report.md      # Phase 6 audit report
├── research/
│   └── [slug]/
│       ├── sources.md
│       └── quotes/
├── images/
│   └── [slug]/
│       ├── hero-v1.png, hero-v2.png, hero-v3.png
│       └── hero-final.png
├── published/                        # Archive after shipping
└── voice-samples/                    # Reference writing
```

Final MDX lives in `~/vibecode-projects/steelmotion/content/blog/[slug].mdx` (where Velite expects it). The `articles/` tree is the working copy and audit trail.

---

## PHASE 0: INTAKE

Parse the brief. Ask only if missing:

```
Intake for new article

Topic:       [what is this about]
Angle:       [the specific claim - ask if user says "not sure yet"]
Reader:      [ONE named person this is for, not a persona]
Platform:    Website (steelmotionllc.com) → default. Substack comes later.
Goal:        Thought leadership / analysis / education / case study
Length:      Flagship (~20 min, 25-32K chars) or Standard (~10 min, 10-15K chars)
Research:    Quick (3 sources) / Standard (7) / Deep (15+, primary-source majority)
Counter?:    Include counterargument section? Yes/No/ask-me-later
```

### Create the working directory

```bash
SLUG=$(echo "$TITLE" | tr 'A-Z' 'a-z' | sed -e 's/[^a-z0-9-]/-/g' -e 's/--*/-/g' -e 's/^-//' -e 's/-$//')
DATE=$(date +%Y-%m-%d)
mkdir -p ~/vibecode-projects/steelmotion/articles/drafts/${DATE}-${SLUG}
mkdir -p ~/vibecode-projects/steelmotion/articles/research/${SLUG}
mkdir -p ~/vibecode-projects/steelmotion/articles/images/${SLUG}
```

### Load the voice fingerprint

```bash
cat ~/vibecode-projects/steelmotion/articles/VOICE.md
```

This MUST be read this session. No drafting proceeds without it.

---

## PHASE 1: ANGLE (HARD GATE)

**Before any research, the angle must be specific and approved.**

Generic: "Article about AI coding tools"
Specific: "Vibe coding is a misnomer; what professionals do is vibe engineering, and the distinction determines whether code survives production."

### Socratic angle questions (one at a time)

1. What is the SPECIFIC claim this article is making? One sentence.
2. What conventional wisdom does this push against? Name it.
3. What would a reasonable person who disagrees say? You must know the counter even if you do not include a counterargument section.
4. What is the ONE thing the reader will walk away knowing that they did not before?
5. What pattern does this article fit: Terminology Correction / Generational Frame / Reality Check / Original Analysis / Case Study?

### Save the angle

`articles/drafts/YYYY-MM-DD-[slug]/angle.md`

Structure:
```markdown
# Angle: [title placeholder]

## The Claim
[One-sentence specific claim]

## What It Pushes Against
[The conventional wisdom this article challenges]

## Counterargument (if included)
[Summary of the reasonable disagreement]

## Reader Walks Away Knowing
[The ONE specific takeaway]

## Pattern
[Terminology / Generational / Reality-Check / Analysis / Case-Study]

## Target Reader
[Name, role, specific characteristics]
```

### HARD GATE: User approves the angle

Do NOT proceed to research until the user locks the angle.

Common rejections at this gate:
- "Too generic, sharpen the claim"
- "The counter is weaker than that, find a better one"
- "Wrong pattern, this is more analysis than correction"

Revise and re-present until approved.

---

## PHASE 2: RESEARCH

Three tiers, set in Phase 0:

| Tier | Sources | Primary-Source % | Time | Use For |
|------|---------|-----------------|------|---------|
| Quick | 3 minimum | 30% | 15 min | Opinion pieces, personal takes |
| Standard | 7 minimum | 50% | 45 min | Industry analysis, explainers |
| Deep | 15+ | 70% | 2+ hrs | Original analysis, contrarian takes |

### Source requirements

Each source must have:
- **URL** (live, not 404)
- **Author** (real name, not "team" or "staff")
- **Date** published (specific, not "recently")
- **Credential** (what makes their view relevant)
- **Key quote or data point** (the specific thing being cited)
- **Type:** primary (researcher's paper, company filing, person's own quote) or secondary (reporter's summary)

### Dispatch research sub-agent

Task tool with model=sonnet; dispatch one or multiple sub-agents in parallel for different aspects of the topic. Each sub-agent uses `/scrape` for content extraction and `web_fetch`/`WebSearch` as needed. They write full source notes to `articles/research/[slug]/` and return <500 tokens.

### Consolidate research

`articles/drafts/YYYY-MM-DD-[slug]/research.md` organizes the usable quotes and data by planned article section.

### Counterargument capture (if enabled)

If counterargument section is included, explicitly dedicate a subsection of research.md to the strongest disagreeing voices. Name them with credentials. Use their actual words.

---

## PHASE 3: OUTLINE (HARD GATE)

Produce the outline before any prose.

### Structure options based on the Phase 1 pattern

**Pattern: Terminology Correction**
1. The common term, used loosely
2. Where it came from (originator named, dated)
3. What got lost in translation
4. The corrective term (and who coined it)
5. What changes when you use the right term
6. Action for the reader

**Pattern: Reality Check**
1. The optimistic narrative (specific claims)
2. The contradiction point (named source)
3. What the data actually shows
4. What the narrative got right
5. What the narrative missed
6. What to believe now

**Pattern: Original Analysis**
1. Setup (the phenomenon)
2. Data (numbers, named sources)
3. The insight (non-obvious interpretation)
4. Implications (what this means for practitioners)
5. Objections anticipated
6. Action

### Outline file format

```markdown
# Outline: [title]

## Hook (2-4 paragraphs)
[Specific scene, observation, or moment. Not abstract.]

## Section 1: [Descriptive header that advances argument]
- Key point:
- Evidence: [source from research.md]
- Transition to next:

## Section 2: [Descriptive header]
- Key point:
- Evidence:
- Transition:

[... continue]

## Section N (closer): [Descriptive header]
- Land the thesis firmly
- NO summary of sections
- NO inspirational quote

## Target Length: [N] words / [M] min read
```

### HARD GATE: User approves the outline

Common rejections:
- "Section 3 is weak, pull it"
- "Hook is too abstract, make it a specific moment"
- "You're burying the thesis, state it by section 2"

Revise until approved.

---

## PHASE 4: DRAFT LONG

Write 2-3x the target final length. Cutting is easier than expanding.

### Drafting rules (strict)

1. **Voice from VOICE.md.** Varied sentence rhythm. Fragments allowed. Short paragraphs OK.
2. **Specific over generic.** Name companies, people, products. Not "large tech companies" but "Anthropic, OpenAI, Google."
3. **Sources in-line.** Every quote attributed where it appears.
4. **Commit to positions.** Strong claims default. Hedging reserved for genuine uncertainty.
5. **No forbidden phrases.** (See Phase 6 list.)
6. **One thought per paragraph.**
7. **Transitions by content, not connective tissue.** Do not write "Moreover" or "Furthermore."

### Save the draft

`articles/drafts/YYYY-MM-DD-[slug]/draft-v1-long.md`

Include frontmatter block at the top (will be copied to final).

---

## PHASE 5: VOICE PASS

Compare draft against VOICE.md fingerprint.

Check:
- Opening sentence: concrete, not abstract
- Sentence rhythm: varied (short + long + fragment)
- Authority posture: direct, committed, not hedging on clear issues
- Citation style: every external claim has a named source with credential and date
- Vocabulary: zero forbidden phrases
- Formatting: italics for emphasis, bold for defined terms, block quotes for long citations
- Reader relationship: peer, not student

Deviations flagged with location, pattern violated, suggested fix. Apply fixes in-place. Save as `draft-v2-voice.md`.

---

## PHASE 6: ANTI-SLOP SCRUB

**This is the whole point.** Automated pattern detection against a comprehensive banned list.

### Banned phrase scan (run against the draft)

```bash
DRAFT=articles/drafts/YYYY-MM-DD-[slug]/draft-v2-voice.md

# Banned opening/throat-clearing
grep -niE "in today's [a-z]+ world|in an era of|it's worth noting|let's (dive|explore|unpack|take a look)|picture this:|what if i told you|here's the thing" "$DRAFT"

# Banned transitions (at sentence start)
grep -niE "^(moreover|furthermore|that said|at the end of the day|in conclusion)\b" "$DRAFT"

# Banned corporate-speak
grep -niE "\b(leverage|robust|cutting-edge|state-of-the-art|seamless|streamline|delve|navigate [a-z]+|unlock [a-z]+|embrace [a-z]+)\b" "$DRAFT"

# Banned AI signatures
grep -niE "not [a-z]+, but [a-z]+|this is where [a-z]+ comes in|the rest is history|only time will tell|right\?\s*$"  "$DRAFT"

# Banned closings
grep -niE "in conclusion|to conclude|in summary|to summarize" "$DRAFT"

# Unearned authority
grep -niE "studies show|research (shows|indicates|suggests)|experts (agree|say)|scientists (have found|agree)" "$DRAFT"

# Em dashes (absolute ban per user preference)
grep -nE "—|–" "$DRAFT"

# Round statistics (require specific %)
grep -niE "\babout (half|a third|two-thirds|a quarter)|\baround [0-9]+%|\broughly [0-9]+" "$DRAFT"

# Stacked parallel structure
grep -niE "not [^,]+, but [^.]+\. not [^,]+, but" "$DRAFT"
```

Each flagged line must be rewritten or explicitly justified in the scrub report.

### Structural anti-slop checks (manual, documented)

- [ ] Hook is specific (scene/person/number), NOT abstract
- [ ] No section has exactly 3 bullet points just because 3 is a comfortable number
- [ ] Paragraph lengths vary (not all 3-5 sentences)
- [ ] Intro does NOT state what the article will cover
- [ ] No section ends with a summary sentence restating what was said
- [ ] Conclusion does NOT restate everything
- [ ] Perfect parallel lists softened where unnatural
- [ ] Transitions by content, not connective adverbs
- [ ] Closing is a strong claim, not a quote or inspirational line

### Research integrity checks

- [ ] Every stat has a specific year
- [ ] Every stat has a methodology note or source
- [ ] Every named source has a credential/context
- [ ] No "studies show" without the study named
- [ ] At least one contradicting source cited (Standard/Deep tier only)

### Scrub report

`articles/drafts/YYYY-MM-DD-[slug]/slop-scrub-report.md`:

```markdown
# Anti-Slop Scrub Report - [slug]

## Phrase Violations Found
| Line | Pattern | Original | Fix Applied |

Total caught: [N]
All fixed: YES / NO (if NO, explain)

## Structural Checks
[Checklist with pass/fail + notes]

## Research Integrity
[Checklist with pass/fail + notes]
```

Save scrubbed version as `draft-v3-scrubbed.md`.

---

## PHASE 7: CUT (HARD GATE)

Target: 50% of Phase 4 long draft is cut.

### Cutting principles

- **Kill paragraphs before killing sentences.** A paragraph that does not earn space goes entirely.
- **Compress repetition.** Two paragraphs making the same point get reduced to one.
- **Axe examples that do not advance the argument.**
- **Remove transition scaffolding.** "As we saw above, X" rarely earns its space.
- **Trim quote lengths.** Long block quotes trimmed to the essential claim.

### Save as `draft-v4-cut.md`. Report before/after word count.

### HARD GATE: User approves the cut draft

Last chance to reshape. Common rejections:
- "You cut the best example, restore it"
- "Still too long, cut another 500 words"
- "Closing needs more punch"

---

## PHASE 8: FACT PASS

Independent verification via sub-agent.

Dispatch a sonnet sub-agent to fact-check every specific claim: numerical accuracy, name spelling, title/credential accuracy as of the date, verbatim quotes, live URLs.

Save fact-check report to `articles/drafts/[slug]/fact-check.md`. Apply corrections. Save final as `draft-v5-fact-checked.md`.

---

## PHASE 9: IMAGE GENERATION (Fal.ai Flux Pro)

### Setup

Fal.ai credentials in `~/vibecode-projects/prophet-a-novel/.env` as `FAL_KEY`. Syncs into `~/vibecode-projects/steelmotion/.env.local` on first use:

```bash
if ! grep -q FAL_KEY ~/vibecode-projects/steelmotion/.env.local 2>/dev/null; then
  FAL_KEY=$(grep FAL_KEY ~/vibecode-projects/prophet-a-novel/.env | cut -d= -f2-)
  echo "FAL_KEY=${FAL_KEY}" >> ~/vibecode-projects/steelmotion/.env.local
fi
```

### Prompt crafting

Read the draft. Extract the CONCEPT, not literal content. Hero image evokes the article's territory.

Approach:
- Identify 2-3 visual metaphors matching the article's thesis
- Draft a prompt using visual language: scene, subject, lighting, mood, composition
- Match art direction to existing site images in `steelmotion/public/images/blog/`

Art direction defaults:
- Photorealistic or cinematic illustration
- Desaturated with 1-2 accent colors
- Strong single subject or focused composition
- NOT infographic, NOT charts, NOT stock-photo vibe

### Generate 3 candidates via Python

Adapt `gen_art.py` from `~/vibecode-projects/prophet-a-novel/02. docs/prompts-and-tools/gen_art.py`:

```python
import fal_client, os, urllib.request
from dotenv import load_dotenv
load_dotenv(os.path.expanduser('~/vibecode-projects/steelmotion/.env.local'))

def generate_hero(prompt, slug, variant):
    handler = fal_client.submit(
        "fal-ai/flux-pro/v1.1-ultra",
        arguments={
            "prompt": prompt,
            "num_images": 1,
            "aspect_ratio": "16:9",
            "output_format": "png",
            "safety_tolerance": "3",
            "raw": False,
        },
    )
    result = handler.get()
    url = result["images"][0]["url"]
    out_path = os.path.expanduser(
        f"~/vibecode-projects/steelmotion/articles/images/{slug}/hero-v{variant}.png"
    )
    urllib.request.urlretrieve(url, out_path)
    return out_path

for i, p in enumerate([prompt_v1, prompt_v2, prompt_v3], 1):
    generate_hero(p, slug, i)
```

### Present candidates; user picks one

### Final image placement

1. Copy chosen to `~/vibecode-projects/steelmotion/public/images/blog/[slug].png`
2. Update MDX frontmatter: `image: /images/blog/[slug].png`
3. Save chosen as `articles/images/[slug]/hero-final.png`

---

## PHASE 10: PUBLISH

### Website (primary)

1. Copy `draft-v5-fact-checked.md` → `~/vibecode-projects/steelmotion/content/blog/[slug].mdx`
2. Verify Velite frontmatter matches schema (title, slug, description, date, author, category, categoryColor, tags, image, imageAlt, readTime, featured, published)
3. Run `pnpm build` locally to verify render
4. Hand off to `/gh-ship` for commit + push + deploy

### Substack (deferred handoff)

Trigger: user says "push [article] to Substack" after the website version has been live.

Substack does NOT have a public posting API for long-form articles. Options:

1. **HTML export + manual paste** (default)
   - Generate `articles/drafts/[slug]/substack.html` - ready to paste
   - User opens Substack editor, pastes body, sets title/subtitle/cover manually
   - Publishes

2. **Browser automation** (optional)
   - Use `/browse` via Playwright MCP
   - Navigate to Substack editor, paste, submit

3. **Email-to-Substack** (paid plans only)
   - If enabled, send article to user's private publish address

Generate both deliverables:
- `articles/drafts/[slug]/substack.html`
- `articles/drafts/[slug]/substack-instructions.md`

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Blog-specific cleanup

1. **Drafts preserved.** `articles/drafts/[slug]/` stays - audit trail.
2. **Research preserved.** `articles/research/[slug]/` stays.
3. **Image candidates preserved.** Unused hero candidates remain in `articles/images/[slug]/`.
4. **Published copy archived.** Final MDX copied to `articles/published/YYYY-MM-DD-[slug].mdx`.
5. **Temp files removed.** `/tmp/` artifacts from image download or fact-check cleaned.
6. **.gitignore enforcement.** `articles/` is gitignored by default; `content/blog/` (Velite source) is committed.

---

## SITREP

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```
═══════════════════════════════════════════════════════════════
SITREP - /blog
═══════════════════════════════════════════════════════════════

  Title:          [article title]
  Slug:           [url-slug]
  Platform:       Website (live at /blog/[slug])
  Next:           Substack push available with: /blog --substack [slug]

  Word Count:     Draft v1: [X] → Final: [Y] ([Z]% cut)
  Sources:        [N] total ([M] primary, [K] disagreeing)
  Images:         [1] hero (Fal.ai Flux Pro)

  Anti-Slop:      [N] violations caught, all fixed
  Fact-Check:     [N] claims verified, [M] corrections applied
  Voice:          Passed fingerprint check

  Files:
    Draft trail:  articles/drafts/YYYY-MM-DD-[slug]/
    Research:     articles/research/[slug]/
    MDX:          content/blog/[slug].mdx
    Hero image:   public/images/blog/[slug].png

  Deploy:         Triggered via /gh-ship → commit [sha]
  URL:            https://steelmotionllc.com/blog/[slug]

═══════════════════════════════════════════════════════════════
                Next: /gh-ship to ship · /monitor to verify
═══════════════════════════════════════════════════════════════
```

---

## RELATED SKILLS

**Feeds from:**
- `/brainstorm` - When the topic itself needs Socratic exploration before becoming an article
- `/narrative` - Storytelling scaffold when the piece is story-driven, not data-driven
- `/marketing` - Content supporting a broader campaign uses the approved messaging framework

**Feeds into:**
- `/gh-ship` - After Phase 10, /gh-ship commits + pushes + deploys
- `/monitor` - Post-deploy, verify the article renders at the live URL
- `/social` - Once published, generate platform-native social posts distributing the article

**Pairs with:**
- `/narrative` - Called inline during Phase 4 when a section needs storytelling craft
- `/copy` - When the article teases a gated asset, copy for the CTA comes from there
- `/scrape` - Used during Phase 2 research for extracting content from web sources

**Auto-suggest after completion:**
- `/gh-ship` - "Draft ready. Want to ship to production?"
- `/social` - "Article live. Want platform-native posts for LinkedIn, X, Threads?"
- `/blog --substack [slug]` (after N days) - "Ready to push to Substack?"

---

## MODES

```bash
/blog                          # Full pipeline from topic to published
/blog [topic]                  # Same, with topic pre-filled
/blog --research-only [topic]  # Stop after Phase 2 (research, no drafting)
/blog --outline-only [topic]   # Stop after Phase 3 (outline, no drafting)
/blog --draft [slug]           # Resume from existing angle/outline, start drafting
/blog --substack [slug]        # Generate Substack deliverables for existing article
/blog --regen-image [slug]     # Re-run Phase 9 for new hero candidates
/blog --quick-scrub [slug]     # Run just Phase 6 on an existing draft (maintenance)
```

---

## REMEMBER

- **Hard gates are hard.** Phase 1 (angle), Phase 3 (outline), Phase 7 (cut draft) require user approval. Do not proceed past a gate without it.
- **VOICE.md is mandatory load.** Every draft session reads it. No exceptions.
- **Anti-slop scrub is not optional.** Run it on the full draft, every time.
- **Every claim has a named source.** "Experts say" is banned.
- **Reader specificity required.** One person, not a persona.
- **Counterargument is opt-in per article type.** Terminology Corrections and Reality Checks usually need it; Case Studies often do not.
- **Hero image ships with the article.** Do not let it slip to "I will add one later."
- **Website first, Substack N days later.** Handoff is explicit, not automatic.

ARGUMENTS: $ARGUMENTS

<!-- /blog v2 - long-form article pipeline with anti-slop discipline + voice fingerprinting + Fal.ai imagery -->
<!-- Hero image generation adapts prophet-a-novel/02. docs/prompts-and-tools/gen_art.py -->
