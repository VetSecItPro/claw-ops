---
name: Playbook Standard
version: 1.0
description: Chunk-ready, RAG-adapted structure for all agent playbooks in IronWorks Knowledge Base
applies_to: All knowledge_pages with document_type='playbook'
---

# Playbook Standard — Chunk-Ready, RAG-Adapted

**Purpose:** Every playbook in the IronWorks Knowledge Base follows this structure so that:

1. **Agents can scan fast** — TOC + H2 structure lets an agent find the right section without loading the whole playbook
2. **RAG can chunk cleanly** — each H2 section is a self-contained semantic unit, embedded independently
3. **Lazy loading works** — agent runtime can fetch one section by anchor, not the entire doc
4. **Output quality improves** — Ollama Cloud models perform better on focused, short context than bloated prompts
5. **Cross-agent discovery works** — consistent structure means any agent can read any other agent's playbook

**Constraint awareness (Ollama Cloud):** IronWorks runs on Ollama Cloud, which does NOT support Anthropic-style `cache_control` prompt caching. The only lever for reducing per-call tokens is sending less context. This standard exists to make "less context" possible without losing capability.

---

## 1. Required Frontmatter

Every playbook starts with YAML frontmatter. Fields are stored in `knowledge_pages` columns where applicable, but also duplicated in frontmatter so the page is self-describing when exported.

```yaml
---
title: {{Playbook title — matches knowledge_pages.title}}
slug: {{slug — matches knowledge_pages.slug, kebab-case}}
document_type: playbook
department: {{engineering | marketing | operations | finance | hr | security | compliance | executive}}
owner_role: {{CMO | CFO | CTO | COO | VP HR | Security Lead | QA Lead | etc.}}
audience: {{single-role | multi-role | all-agents}}
version: {{1.0}}
last_reviewed: {{YYYY-MM-DD}}
tags: [{{tag1}}, {{tag2}}, {{tag3}}]
related_playbooks: [{{slug1}}, {{slug2}}]
feeds_from: [{{slug}}]
feeds_into: [{{slug}}]
chunk_strategy: h2
estimated_tokens: {{approx total tokens — used for lazy-load decisions}}
---
```

**Field rules:**

- `audience: single-role` — only one department reads this (e.g., CFO-only)
- `audience: multi-role` — specific list of roles (list them in `owner_role` as comma-separated)
- `audience: all-agents` — universal reference (e.g., writing style guide)
- `chunk_strategy: h2` — default; every H2 becomes one chunk. Use `chunk_strategy: flat` only for playbooks under 2k tokens where chunking adds no value.

---

## 2. Required Top-Level Structure

Every playbook has these sections in this exact order:

```markdown
# {{Playbook Title}}

**One-line purpose statement.** {{What this playbook enables the agent to do}}

**When to load this playbook:**
- {{Trigger signal 1}}
- {{Trigger signal 2}}
- {{Trigger signal 3}}

**When NOT to load this playbook:**
- {{Anti-signal 1 — load {{other playbook}} instead}}
- {{Anti-signal 2}}

---

## Table of Contents

- [TL;DR](#tldr)
- [Core Principles](#core-principles)
- [{{Domain Section 1}}](#{{anchor1}})
- [{{Domain Section 2}}](#{{anchor2}})
- ...
- [Anti-Patterns](#anti-patterns)
- [Output & Filing](#output-filing)
- [SITREP](#sitrep)
- [Related Playbooks](#related-playbooks)

---

## TL;DR

**3-5 bullets. Answers: what does this playbook do, when does it fire, what's the output.**

- {{Bullet 1 — the discipline this enforces}}
- {{Bullet 2 — the primary output}}
- {{Bullet 3 — the hard rule that cannot be skipped}}
- {{Bullet 4 — the trigger signal}}
- {{Bullet 5 — the escape hatch / related playbook}}

The TL;DR is the FIRST chunk RAG returns on a general query. It must stand alone.

---

## Core Principles

**3-7 non-negotiable rules. Each is a one-liner with a brief "why".**

1. **{{Principle 1}}** — {{one-sentence reason}}
2. **{{Principle 2}}** — {{one-sentence reason}}
3. **{{Principle 3}}** — {{one-sentence reason}}

These are what the agent MUST hold while executing. Rationalization tables later in the playbook reference these by number.

---

## {{Domain Section 1}}

{{Content — sized to fit in one chunk, typically 400-1500 tokens}}

### Subsection (H3) if needed

{{H3 is fine inside an H2 — it stays in the same chunk}}

---

## {{Domain Section 2}}

{{...}}

---

[... more domain sections as needed ...]

---

## Anti-Patterns

**Rationalizations the agent will be tempted by, and how to reject them.**

| Rationalization | Reality | What to Do |
|-----------------|---------|------------|
| "{{Quoted internal voice}}" | {{Why it's wrong}} | {{Specific action}} |
| "{{Quoted internal voice}}" | {{Why it's wrong}} | {{Specific action}} |

Every playbook has at least 3 anti-patterns. Hardened playbooks have 6+.

---

## Output & Filing

**Where the agent files its work product when using this playbook.**

### File Location

Work products go to the IronWorks Library at:

```
library/
├── projects/
│   └── {{project-slug}}/
│       ├── briefs/              # Input specs, requirements
│       ├── deliverables/        # Final work products (reports, designs, copy)
│       ├── reports/             # Status, SITREPs, audits
│       └── working/             # Drafts, WIP (auto-archive after 30 days)
├── shared/
│   ├── templates/               # Reusable templates
│   ├── brand/                   # Brand guidelines, voice, assets
│   └── references/              # External research, competitor analysis
└── departments/
    └── {{department}}/          # Department-internal docs
```

### Naming Convention

`{{YYYY-MM-DD}}-{{slug}}-{{version}}.md`

Example: `2026-04-11-q2-launch-brief-v1.md`

### Required Metadata

Every work product has frontmatter:

```yaml
---
title: {{Title}}
type: {{brief | deliverable | report | draft}}
project: {{project-slug}}
author_agent: {{agent-name}}
author_agent_id: {{uuid}}
created_at: {{ISO timestamp}}
status: {{draft | review | approved | shipped | archived}}
source_playbook: {{playbook-slug}}
related: [{{other-file-slugs}}]
---
```

---

## SITREP

**How the agent reports completion. Reference: ~/.claude/standards/SITREP_FORMAT.md**

```markdown
## SITREP — {{Playbook Name}}

**Mission:** {{one line}}
**Status:** COMPLETE | IN_PROGRESS | BLOCKED
**Duration:** {{X min}}

### What Was Delivered
- {{artifact 1 — link to Library path}}
- {{artifact 2}}

### Decisions Made
- {{decision 1 — rationale}}

### Blockers / Risks
- {{none | list}}

### Handoff
- Next: {{next playbook or agent}}
- Review needed: {{yes/no — who}}
```

---

## Related Playbooks

**Feeds from:** {{playbooks whose output this consumes}}
**Feeds into:** {{playbooks that consume this output}}
**Pairs with:** {{playbooks commonly run together}}
**Alternative to:** {{playbooks that solve similar problems differently}}

---

## 3. Chunking Rules

**Each H2 section is one chunk.** Rules for chunk authors:

- **Token budget per chunk:** 200-2000 tokens. Sweet spot ~800.
- **Self-contained:** A chunk must make sense WITHOUT the surrounding sections. Define acronyms. Don't say "as discussed above."
- **Keywords in heading:** The H2 heading is the primary signal for RAG retrieval. Make it specific: "Cost Attribution Model" not "The Model."
- **Action-oriented bullets:** RAG retrieves better on imperative verbs than abstract nouns.
- **Never split a procedure across chunks:** If a 10-step procedure is 3000 tokens, make it ONE chunk (exception to the 2000 limit). Procedures must be atomic.

### What Counts as a Good H2 Heading

Good:
- `## Cost Attribution Model`
- `## Handling Escalation: Severity 1 Incidents`
- `## Weekly Marketing Sync Agenda`

Bad:
- `## Overview` (too generic — won't retrieve)
- `## Part 2` (meaningless)
- `## More Details` (no signal)

---

## 4. Chunk Metadata (Applied by RAG Pipeline)

When the RAG indexer processes a playbook, it writes these fields per chunk:

```typescript
{
  chunk_id: uuid,
  page_id: uuid,         // FK to knowledge_pages
  anchor: string,        // "#cost-attribution-model" — for direct fetch
  heading: string,       // "Cost Attribution Model"
  heading_path: string,  // "CFO Playbook > Cost Attribution Model"
  body: string,          // chunk content (200-2000 tokens)
  token_count: number,
  embedding: vector(768),  // pgvector, Ollama embedding model
  order: number,         // position in doc
  department: string,    // inherited from page
  owner_role: string,    // inherited
  audience: string,      // inherited
  related_chunks: uuid[] // siblings for multi-hop retrieval
}
```

**Embedding model:** Use Ollama's `nomic-embed-text` (768d) — free, local on the VPS, no Ollama Cloud tokens consumed.

---

## 5. Agent Lookup Protocol

Agents access playbook content via the `lookup_playbook` tool, not by loading the full doc:

```typescript
// Agent calls this, not a full doc fetch
lookup_playbook({
  query: "how do I handle a severity 1 incident?",
  department: "operations",  // optional filter
  owner_role: "COO",         // optional filter
  top_k: 3                   // default 3 chunks
})

// Returns: 3 most relevant chunks with heading_path + body
```

This is the primary cost-saver. A 60k-token playbook loaded on every call = 60k tokens × 100 agent runs/day = 6M tokens/day of redundant context. A `lookup_playbook` returning 3 chunks = ~3k tokens per call.

---

## 6. When to Load the FULL Playbook

Sometimes lookup isn't enough:

- **First-time learning:** A new agent joining the company reads the full playbook once, caches key principles in agent-level memory, then uses `lookup_playbook` for specifics going forward.
- **Comprehensive audits:** When the full surface of a domain needs coverage (e.g., CFO running a full-year review).
- **Short playbooks (<3k tokens):** Chunking overhead isn't worth it.

The agent runtime tracks which playbooks were loaded in the current session (~1 hour) and skips reload within that window.

---

## 7. Validation Checklist

Before a playbook is considered compliant:

- [ ] Frontmatter present with all required fields
- [ ] TL;DR section ≤ 5 bullets, self-contained
- [ ] Core Principles section with 3-7 numbered rules
- [ ] Every H2 heading is specific (not "Overview", "Part 2")
- [ ] No H2 chunk exceeds 2000 tokens (except atomic procedures)
- [ ] Anti-Patterns table with ≥ 3 rows
- [ ] Output & Filing section with Library path + naming convention
- [ ] SITREP format defined
- [ ] Related Playbooks section filled (or explicitly "none")
- [ ] No em dashes in body (per project style rules)
- [ ] No sparkle icons
- [ ] Generic placeholders `{{}}` used — no Steel Motion / IronWorks-specific refs unless the playbook IS about IronWorks itself

---

## 8. Migration from Legacy Playbooks

Legacy playbooks (pre-standard) typically lack:
- TOC
- Clear H2 chunking
- Anti-Patterns table
- Output & Filing section

**Migration process:**

1. Read existing playbook body
2. Extract: purpose, principles, domain content, examples
3. Restructure into H2 sections with specific headings
4. Add TL;DR (synthesize from existing intro)
5. Add Core Principles (extract from rules scattered in body)
6. Add Anti-Patterns (extract from warnings/cautions, or author new ones based on known failure modes)
7. Add Output & Filing (if missing, apply default Library path for the department)
8. Add SITREP format (use standard template)
9. Validate against checklist above
10. UPDATE knowledge_pages.body with new structure; revision_number auto-increments

---

## 9. Ownership & Updates

- **Playbook owner:** The agent whose role matches `owner_role`
- **Review cadence:** Quarterly — owner agent re-reviews their playbook and files a SITREP if changes needed
- **CTO (technical leadership agent) reviews ALL playbooks** at the quarterly cycle for cross-cutting consistency
- **Version bumps:** Bump `version` in frontmatter on any material content change; bump `last_reviewed` on review-only passes

---

## 10. Related Standards

- `~/.claude/standards/SKILL_PATTERN.md` — for global Claude Code skills (different from playbooks)
- `~/.claude/standards/SITREP_FORMAT.md` — referenced by playbook SITREP section
- `~/.claude/standards/CONTEXT_MANAGEMENT.md` — playbooks feed into context budgets
- `~/.claude/standards/STEEL_DISCIPLINE.md` — rationalization table methodology

---

## Evolution

- **v1.0 (2026-04-11):** Initial standard. Chunk-ready H2 structure, RAG-adapted frontmatter, Library filing conventions, Ollama Cloud constraints acknowledged.
