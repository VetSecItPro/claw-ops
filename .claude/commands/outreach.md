---
description: "/outreach - DRAFT SPEC. Per-signal personalized outreach drafts (LinkedIn, cold email, X reply) using voice-of-customer language captured by /hunt. Reads hunt signals + ICP + product-brief; outputs reviewable drafts. NOT YET IMPLEMENTED - delivery strategy needs user decision."
allowed-tools: Bash(curl *), Bash(jq *), Bash(ls *), Bash(mkdir *), Bash(cat *), Bash(date *), Read, Write, Edit, Glob, Grep, Task, WebFetch
---

# /outreach - Personalized Cross-Channel DM/Email Drafting

> **DRAFT SPEC - NOT YET IMPLEMENTED.**
> This file describes the contract and behavior of `/outreach`. It does NOT execute. Implementation is gated on user decisions flagged in the "Open Questions" section below.
> Why ship the spec now: by the time we build it, the structure (DISCIPLINE, SITREP, scoring contract, etc.) is already set. No rewrite, no re-think.

---

## What This Skill Does (intended behavior)

Takes the Tier 1 signals from `/hunt`, the ICP from `/prospect`, and the product brief from `/icp-from-repo`; produces personalized outreach drafts that quote the prospect's exact pain language back to them. Channel-aware: LinkedIn connect requests, 3-touch cold email sequences, X/Twitter replies. No volume play. Hyper-personalized.

**Reference school:** Chris Corner (voice-of-customer mining), Nick Saraev (hyper-personalized cold), Alex Hormozi (offer + framing). Common thread: never write to a stranger using your words; always quote theirs back.

---

## DISCIPLINE

> Reference: [Steel Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

**Steel Principle #1:** No outreach draft without a backing signal. Every draft must reference a specific `signals.json` row by id. No "spray a generic message" mode. Ever.

**Steel Principle #2:** Quote, do not paraphrase. The prospect's exact words go into the draft, in quotes, attributed back to the source URL. Paraphrasing the pain breaks the entire personalization mechanic.

**Steel Principle #3:** One CTA, low friction. Asking for a 30-min call in touch #1 is a fast no. Touch #1 asks for a yes/no opinion or a single resource share. Calls come on touch #3 or later.

**Steel Principle #4:** Never auto-send. Drafts go to a review folder. the user (or user) sends manually. The skill never holds delivery credentials in v1.

**Steel Principle #5:** Channel etiquette is non-negotiable. LinkedIn connect note = 200-300 chars max. Cold email = 90 words max for touch #1. X reply = 280 chars + adds value to the public thread first. Violations get the account flagged or banned.

### Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "Quoting feels weird, paraphrase is smoother" | Paraphrasing IS the generic-template tell. The exact-quote is what makes them stop scrolling | Quote with attribution. Awkward beats invisible. |
| "30-min call CTA is industry standard" | It's the standard reason cold outreach has 1% reply rates. The standard is broken | Touch 1 = micro-yes. Touch 3 = call. |
| "I'll let it rip across 500 prospects" | Volume play is the OLD model. Hyper-personal is the new model. Even 50/week beats 500 generic | Cap at 30 prospects per run. Quality ratchet is the moat. |
| "Generic 'hope this helps' template is fine" | If the prospect can paste your message into ChatGPT and say "did you write this?" and it says yes - you lost | Read like a human who actually read the signal |
| "LinkedIn connect note is just a connect note, skip the personalization" | The connect note IS the personalization. No note = ignored. Generic note = "report as spam" | 200 chars, quotes the signal, no pitch |
| "I'll mention the product in the connect note" | LinkedIn auto-rejects (and shadow-bans the sender) connect notes that read as pitches | Connect = pure value. Pitch comes after they accept. |
| "Email touch #1 needs a calendar link" | Calendar links in cold #1 = mark as spam. Most email providers downrank as well | No calendar link until touch #3 |
| "X replies are quick wins, don't need to read the thread" | Replying without context = obvious bot behavior; thread participants pile on | Read the full thread (and 1-2 of the OP's recent posts) before replying |
| "Skip the source URL in the draft, the user knows where it came from" | Without the source URL, the user can't sanity-check the quote in 5 seconds before sending | Source URL is mandatory in every draft |

---

## STATUS UPDATES

> Reference: [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)

```markdown
🚀 /outreach Started
   Source hunt:  vault/leads/hunt/[date]/signals.json ([N] Tier 1)
   ICP:          [digest]
   Product:      [name from product-brief]
   Channels:     [LinkedIn, Email, X]

🔍 Phase 1: Context Load
   ├─ signals.json:      [N] Tier 1 signals
   ├─ icp.json:          loaded
   └─ product-brief.json: loaded

🎯 Phase 2: Plan + HARD GATE
   ├─ Per-signal channel selection
   └─ ⏸ Awaiting user approval

📝 Phase 3: Per-Signal Drafting
   ├─ [signal-1] LinkedIn connect + email seq
   ├─ [signal-2] X reply only
   └─ ✅ [N] drafts written

🧪 Phase 4: Verify (lint each draft)
   └─ ✅ [N]/[N] pass channel constraints

📊 Phase 5: SITREP + Review Queue
```

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

- Drafts written to `vault/leads/outreach/<date>/<channel>/<prospect-slug>.md`
- No delivery credentials stored anywhere (v1 = manual send)
- No tracking pixels embedded (one of the open questions below)
- Cache has 24h TTL

---

## Input Contract

```
INPUTS:
  vault/leads/hunt/<date>/signals.json     # from /hunt - required
  vault/marketing/icp.json                 # from /prospect - required
  vault/marketing/product-brief.json       # from /icp-from-repo - required
  vault/marketing/sender-profile.json      # NEW - the user's positioning, optional but recommended

CONFIG (Phase 2 prompt):
  channels: [linkedin, email, x]           # subset to draft
  signals: [signal_id, ...]                # subset of Tier 1, default = all
  touches_per_signal: 1-3                  # default = 3 for email, 1 for LI/X
```

## Output Contract

```
vault/leads/outreach/YYYY-MM-DD/
  drafts.json                              # canonical, machine-readable
  review-queue.md                          # human-readable, ranked
  linkedin/
    <prospect-slug>.md                     # connect note + 1-2 follow-ups
  email/
    <prospect-slug>.md                     # 3-touch sequence
  x/
    <prospect-slug>.md                     # public reply + optional DM-after-engagement
```

Per-draft frontmatter:
```yaml
signal_id: <hunt signal id>
signal_url: <source URL - for quote sanity-check>
channel: linkedin|email|x
touch: 1|2|3
quote: "<verbatim from signal>"
icp_match: "<one-line why this prospect fits the ICP>"
cta: "<one-line CTA - must be low-friction>"
status: draft|queued|sent|replied|archived
```

---

## Channels (v1 scope to propose)

### LinkedIn (most-used in DFW agency outbound)

- **Touch 1:** Connect request with personalized note (200-300 chars). Quotes the signal. NO pitch. CTA = implicit (they accept or decline).
- **Touch 2 (after acceptance):** DM with one-paragraph value share + ICP-fit framing. Soft CTA (free resource, not call).
- **Touch 3 (3-5 days later if no reply):** Single-line follow-up with the call ask.

### Cold Email (3-touch sequence)

- **Touch 1 (Day 0):** 90 words max. Quote-from-signal. ICP-fit framing. Micro-yes CTA ("worth chatting?" or "did I read this right?"). NO calendar link.
- **Touch 2 (Day 4):** 60 words. Reframe with a specific resource attached.
- **Touch 3 (Day 9):** 40 words. Direct ask + calendar link. Last touch.

### X / Twitter (reply-first)

- **Touch 1:** Public reply that adds value to the thread. Goes into the user's reply queue, not posted automatically.
- **Touch 2 (after the OP engages with the public reply):** DM with offer.

### Deferred to v2

- Cold call scripts (no spec until decided)
- Instagram DM (low B2B value for current ICPs)
- WhatsApp (compliance burden too high in DFW market)

---

## Personalization Mechanics

For every signal, the draft must include:

1. **Quote-from-signal** - verbatim 1-2 sentences in quotes, attributed to source URL
2. **ICP-match framing** - one sentence connecting the signal to the prospect's likely role/situation
3. **Product-fit phrase** - single sentence linking our product/service to the specific pain (NOT a feature pitch)
4. **Low-friction CTA** - yes/no question, resource share, or "worth a 10-min chat?" - never "30-min discovery call"

Reference patterns (Nick Saraev / Hormozi school):
- Quote + observation: "You said 'X'. Most people who say that are dealing with Y. Is that what's going on for you?"
- Quote + offer: "Saw your comment about 'X'. We made a 5-min loom on exactly this - want it?"
- Quote + identity match: "You called it 'X' - that's the same phrase 3 of our customers used last month. Different industries, same root cause."

---

## Open Questions (gating implementation - needs user decision)

These are explicitly NOT decisions the spec makes. The skill cannot be implemented until each of these is answered.

### Q1: LinkedIn delivery strategy

- (a) **Browser session cookies** (Phantombuster / Dripify / Expandi) - automation-tier, ToS-grey. Risk: account restriction.
- (b) **Sales Navigator API + InMail** - official path, but requires SN seat ($99/mo) AND InMail credits (limited monthly).
- (c) **n8n + LinkedIn community node** - self-hosted, same ToS risk as (a) but the user controls the cadence.
- (d) **Manual copy-paste** - zero risk, zero scale. the user opens the draft folder, copy-pastes into LinkedIn.
- **Recommendation pending user input:** Default to (d) for v1; revisit (b) if SN seat is purchased.

### Q2: Email delivery strategy

- (a) **Bring-your-own SMTP** - Gmail / Google Workspace via app password. Easy, but daily caps (~500/day) and deliverability risk if Gmail flags.
- (b) **Instantly.ai or Smartlead** - purpose-built cold email infra. $97/mo. Deliverability + sequence + warmup. Industry standard for this volume tier.
- (c) **Manual send via Gmail compose** - zero infra, zero scale, zero risk.
- **Recommendation pending user input:** (b) Smartlead/Instantly is the working answer for DFW agency outbound at >50/week.

### Q3: X / Twitter delivery

- (a) **Paid X API** - $200/mo for write access. Allows scheduled replies + DMs.
- (b) **Manual via web** - copy-paste from review queue. Zero cost.
- **Recommendation pending user input:** (b) until X reply volume justifies the API spend.

### Q4: Tracking pixels in email?

- Pro: open/click visibility for cadence triggering
- Con: Apple Mail Privacy Protection blocks pixels for 80%+ of inboxes; some prospects flag the pixel as spam-marker
- **Default proposal:** No pixels in v1. Reply rate is the only metric that matters.

### Q5: How does `/outreach` know which signals are NEW vs. previously drafted?

- (a) State file (`vault/leads/outreach/.state.json`) tracking signal_id → draft status
- (b) Recompute every run (no de-dupe)
- **Default proposal:** (a) - `vault/leads/outreach/.state.json` indexed by signal_id, deduped on each run.

---

## STATUS - Implementation Readiness Checklist

- [ ] Q1 answered (LinkedIn delivery)
- [ ] Q2 answered (Email delivery)
- [ ] Q3 answered (X delivery)
- [ ] Q4 answered (tracking pixels)
- [ ] Q5 answered (de-dupe state)
- [ ] Sender profile schema agreed (`vault/marketing/sender-profile.json`)
- [ ] Channel etiquette constraints reviewed by user (lengths, CTA rules)
- [ ] Approve full spec → implementation begins

---

## SITREP (planned shape - for when implemented)

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md)

```markdown
## SITREP - /outreach

**Source hunt:** vault/leads/hunt/[date]/signals.json
**Tier 1 signals available:** [N]
**Drafts produced:** [N] across [linkedin: N, email: N, x: N]

| Channel | Drafts | Avg length (words) | Constraint pass |
|---------|--------|-------|------|
| LinkedIn | [N] | [N] | [N]/[N] |
| Email    | [N] | [N] | [N]/[N] |
| X        | [N] | [N] | [N]/[N] |

### Top 3 (highest-fit signals first)

1. [signal_id] [channel] - quote: "..." → cta: "..."
2. ...

### Review Queue

vault/leads/outreach/[date]/review-queue.md

### Cleanup

[cache TTL applied; no auto-send; state.json updated]

### Next Steps

1. Open review-queue.md
2. Send the top-N manually (or via configured delivery path once Q1-Q3 answered)
3. Mark sent in state.json so future runs dedupe
```

---

## Related Skills

**Feeds from:** `/hunt` (signals.json), `/prospect` (icp.json), `/icp-from-repo` (product-brief.json)
**Feeds into:** `/copy` (polish a high-stakes single draft), `/social` (cross-channel themes from outreach replies)
**Pairs with:** `/scrape` (deep-dive a prospect company before drafting), `/narrative` (longer-form story angle when a single signal warrants a full narrative)

---

## Why This Spec Exists Before Implementation

Per `feedback_inline_documentation.md` and the Steel Discipline standard: planning artifacts are first-class deliverables. Writing the spec now means:

1. The Open Questions surface NOW, not mid-build
2. The DISCIPLINE / SITREP / cleanup structure is already set - no rewrite when we ship v1
3. `/hunt` can already declare `/outreach` as a downstream consumer with confidence in the input contract
4. When the user (or the user) answers Q1-Q5, building is mechanical, not exploratory
