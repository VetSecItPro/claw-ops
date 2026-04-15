# Skill Router Protocol — Intent to Skill Mapping

**Version:** 1.0
**Applies to:** Every Claude Code session globally
**Companion to:** [Superpowers Discipline](./STEEL_DISCIPLINE.md)

---

## Purpose

The user should never have to memorize skill commands. When the user expresses intent in natural language, Claude should automatically recognize the intent and either:

1. **Auto-invoke** the matching skill (when the match is clear and the user is asking for the outcome the skill delivers)
2. **Suggest** the skill with a one-line explanation and wait for confirmation (when the match is plausible but ambiguous)

This document is the **intent-to-skill mapping table**. Reference it on every user message.

---

## The Core Rule

**Before responding to any user message, check:** does this request match a skill in the table below?

- If YES and the match is obvious (confidence ≥ 80%) → announce the skill and run it
- If YES but ambiguous (confidence 50-80%) → suggest the skill, ask to confirm
- If NO → respond normally

**Do not** list every possible skill the user could run. Match the intent, suggest ONE skill (or at most two alternatives if genuinely comparable).

---

## Intent → Skill Map

### Planning & Thinking

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "I'm thinking about...", "What if we...", "Should we...", "I have an idea...", "Let's explore...", "Should I build..." | `/brainstorm` | High | **Suggest** - "Sounds like a brainstorming session. Want me to run `/brainstorm` to explore this properly?" |
| "Big feature", "new functionality", "architecture change", "multi-tenant", "re-architect", "overhaul", "redesign the system" | `/mdmp` | High | **Suggest** - "This is a significant change. Want me to run `/mdmp` for a structured multi-lens analysis?" |
| "Audit the design system", "design tokens", "make this consistent", "design system audit" | `/design` | Medium | **Suggest** |
| "Are we ready to launch?", "pre-launch check", "production readiness" | `/launch` | High | **Auto** |

### Debugging & Problem-Solving

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Not working", "something's broken", "this bug", "figure out why", "it was working yesterday", "intermittent issue", "flaky test", "can't figure out", "deep dive and investigate" | `/investigate` | **Very High** | **Auto** - "This looks like a bug investigation. Running `/investigate`." |
| "Production is down", "site is broken for users", "users are reporting", "urgent production issue" | `/incident` | **Very High** | **Auto** - "Production issue. Running `/incident` (includes postmortem)." |
| "Performance is slow", "site feels laggy", "Lighthouse score", "bundle size", "LCP", "Core Web Vitals" | `/perf` | High | **Auto** |

### Testing & QA

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Test the whole app", "full QA", "UAT", "end-to-end test", "test all pages" | `/qatest` | High | **Auto** |
| "Quick smoke test", "basic check", "quick sanity check" | `/smoketest` | High | **Auto** |
| "Test coverage", "add tests", "missing tests", "need unit tests", "TDD" | `/test-ship` | High | **Auto** |
| "Red team", "attack", "try to hack", "pen test", "exploitation" | `/redteam` | High | **Suggest** (high-risk tool) |

### Security & Compliance

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Security audit", "find vulnerabilities", "is this secure?", "OWASP", "security scan", "secure the app" | `/sec-ship` | **Very High** | **Auto** |
| "Weekly security scan", "scan all repos" | `/sec-weekly-scan` | High | **Auto** |
| "GDPR", "CCPA", "privacy policy", "data compliance", "cookie consent" | `/compliance` | High | **Auto** |
| "Compliance docs", "enterprise security docs", "vendor questionnaire", "SOC 2 docs" | `/compliance-docs` | High | **Auto** |

### Code Quality & Maintenance

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Clean up code", "technical debt", "dead code", "unused", "simplify", "refactor to clean" | `/cleancode` | High | **Auto** |
| "Update dependencies", "package updates", "check deps", "outdated packages", "CVEs in deps" | `/deps` | High | **Auto** |
| "Major version upgrade", "migrate Next.js", "upgrade React", "breaking changes" | `/migrate` | High | **Auto** |
| "Add documentation", "document this", "generate docs", "why is this here?" | `/docs` | High | **Auto** |
| "Accessibility", "a11y", "WCAG", "screen reader", "keyboard navigation" | `/a11y` | High | **Auto** |

### Database & Data

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Database migration", "schema change", "RLS policy", "seed data", "database drift" | `/db` | High | **Auto** |

### Ship It & Monitor

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Ship it", "commit and push", "create PR", "deploy this", "git flow" | `/gh-ship` | **Very High** | **Auto** |
| "Is production healthy?", "post-deploy check", "is the deploy working?", "health check" | `/monitor` | High | **Auto** |
| "Start dev server", "run the app", "fire up dev" | `/dev` | High | **Auto** |
| "Browser test", "open in browser", "take a screenshot", "navigate to..." | `/browse` | Medium | **Auto** |
| "Publish a blog", "write an article", "content pipeline" | `/blog` | High | **Auto** |

### Marketing & Content

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "Marketing campaign", "go-to-market", "launch strategy", "marketing plan", "channel strategy", "messaging framework", "ICP", "target audience", "GTM" | `/marketing` | High | **Auto** |
| "Write a landing page", "email sequence", "ad copy", "sales page", "CTA copy", "conversion copy", "onboarding copy", "pricing page copy", "rewrite this copy", "write copy for" | `/copy` | **Very High** | **Auto** |
| "Write a post for LinkedIn", "tweet this", "Instagram caption", "social media posts", "content calendar", "post this to social", "social posts", "batch of posts", "TikTok script" | `/social` | **Very High** | **Auto** |
| "Find customers for this product", "find leads for X", "prospect for my SaaS", "score a list of leads", "build an ICP", "scoring model for accounts" | `/prospect` | **Very High** | **Auto** |
| "Extract product brief from this repo", "scan my product code", "auto-fill the ICP from the product", "define ICP from this repo", "read the codebase and tell me who it's for" | `/icp-from-repo` | **Very High** | **Auto** |
| "Find prospects on Reddit", "hunt Reddit for leads", "who's complaining about X on Reddit", "Reddit prospecting", "watch these subreddits for pain signals" | `/reddit-hunt` | **Very High** | **Auto** |

### Meta Skills

| User Intent Signals | Skill | Confidence | Auto/Suggest |
|---------------------|-------|-----------|--------------|
| "I want to build a new skill", "create a custom skill", "add a skill to claude" | `/write-skill` | **Very High** | **Auto** |
| "Implement this plan", "execute the tasks", "build all these features with reviews" | `/subagent-dev` | High | **Auto** |

---

## Pipeline Triggers (Skill Chains)

Some intents trigger a chain of skills. Announce the chain upfront.

### "Build this feature end-to-end"
```
/brainstorm (or /mdmp) → /subagent-dev → /test-ship → /gh-ship → /monitor
```

### "Ship this safely"
```
/test-ship → /sec-ship → /gh-ship → /monitor
```

### "Full audit"
```
/sec-ship → /perf → /a11y → /deps → /cleancode
```

### "Fix production issue"
```
/incident → /investigate → /gh-ship → /monitor
```

### "Launch a product / build a campaign"
```
/icp-from-repo (if product repo is on disk) → /marketing (strategy + channel plan) → /prospect (ICP + scoring) → /reddit-hunt (warm leads with self-identified pain) → /copy (landing page + email) → /social (social calendar) → /blog (launch post)
```

### "Find and engage prospects from scratch"
```
/icp-from-repo → /prospect → /reddit-hunt → /copy (polish top reply drafts)
```

---

## Disambiguation Rules

When multiple skills could match, use these tiebreakers:

### "It's not working"
- Dev server issue? → `/dev`
- Production issue? → `/incident`
- Bug in logic? → `/investigate`
- Test failing? → `/investigate` (tests are bugs until proven otherwise)
- Default: `/investigate`

### "Make this better"
- Better code quality? → `/cleancode`
- Better performance? → `/perf`
- Better security? → `/sec-ship`
- Better UX/design? → `/design`
- Better accessibility? → `/a11y`
- Default: ask which dimension matters

### "Review this"
- Security review? → `/sec-ship --audit`
- Code quality review? → `/cleancode --audit` (or inline review)
- Design review? → `/design --audit`
- Performance review? → `/perf --audit`
- Default: inline code review, no skill invoked

### "I need to plan something"
- Small idea / "thinking about" → `/brainstorm`
- Feature with multiple approaches → `/mdmp` (TACTICAL or OPERATIONAL)
- Architectural change → `/mdmp` (STRATEGIC)
- Default: `/brainstorm` (user can escalate to `/mdmp` if needed)

---

## Announcement Format

When auto-invoking:
```
[Intent detected]
→ Running /[skill] [with flags]
→ [One-line why]
```

Example:
```
Detected: bug investigation request
→ Running /investigate
→ Evidence-first debugging, regression test mandatory
```

When suggesting:
```
This sounds like a [description] task.
→ Want me to run /[skill]?
→ [What the skill will do]

Or I can handle it inline if you prefer.
```

---

## When NOT to Invoke a Skill

Don't auto-invoke when:

1. **User explicitly asks a question** (not a task) - "How does X work?" is a question, not a skill request
2. **User wants a quick answer** - "What's the syntax for..." doesn't need a skill
3. **User is mid-conversation on an unrelated topic** - continuity matters
4. **The task is trivially small** - "Rename this variable" doesn't need `/cleancode`
5. **User has opted out** - if user says "don't use skills" or "just answer me," respect that

---

## User Override Commands

The user can override the router:

| User Says | Meaning |
|-----------|---------|
| "Don't use a skill" | Skip routing, respond directly |
| "Just answer me" | Skip routing, respond directly |
| "Run /[skill] instead" | User picks the skill explicitly |
| "I know you suggested X, but run Y" | User overrides suggestion |
| "Skip the brainstorm, just build it" | Skip planning phase |

---

## Cascading Suggestions

After a skill completes, suggest the logical next skill based on the outcome:

| Skill Finished | Common Next Steps | Suggest |
|----------------|-------------------|--------|
| `/brainstorm` approved | Implementation | "Ready to build? Run `/subagent-dev` with the spec." |
| `/mdmp` COA selected | Implementation | "Plan approved. Run `/subagent-dev` to execute tasks." |
| `/investigate` fix applied | Ship the fix | "Fix verified. Want to run `/gh-ship` to commit and deploy?" |
| `/test-ship` tests added | Ship or security check | "Tests added. Run `/gh-ship`? Or `/sec-ship` first?" |
| `/sec-ship` vulnerabilities fixed | Ship | "Security clean. Run `/gh-ship`?" |
| `/gh-ship` deploy complete | Monitor | "Shipped. Running `/monitor` to verify production health." |
| `/incident` resolved | Review & learn | "Postmortem written. Consider `/test-ship` to add regression tests." |

---

## Confidence Calibration

Examples of high vs. low confidence matches:

**HIGH CONFIDENCE (auto-invoke):**
- "the login form is broken, can't submit" → `/investigate` (specific bug, actionable)
- "ship this to prod" → `/gh-ship` (explicit ship intent)
- "scan for CVEs in our dependencies" → `/deps` (explicit request)

**MEDIUM CONFIDENCE (suggest):**
- "I want to make this faster" → `/perf` (could mean many things)
- "let's clean this up" → `/cleancode` (ambiguous scope)
- "I'm thinking about adding notifications" → `/brainstorm` or `/mdmp`

**LOW CONFIDENCE (don't invoke, just help):**
- "what does this code do?" → explain, don't run skills
- "is this a good approach?" → discuss, don't run `/mdmp`
- "how do I..." → answer, don't invoke

---

## Reference in CLAUDE.md

The user's global `~/.claude/CLAUDE.md` should include a pointer:

```markdown
## Skill Routing

On every user message, check for skill intent using the [Skill Router Protocol](~/.claude/standards/SKILL_ROUTER.md).

Key pattern:
- "I'm thinking about X" → suggest /brainstorm
- "This is broken / not working" → auto /investigate
- "Ship it" → auto /gh-ship
- "Production is down" → auto /incident
- "Is this secure?" → auto /sec-ship

See SKILL_ROUTER.md for the full intent→skill map.
```

---

**Last Updated:** 2026-04-14
