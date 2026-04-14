# Superpowers Discipline Protocol

**Version:** 1.0
**Applies to:** All skills in `~/.claude/commands/`
**Companion to:** [Agent Orchestration](./AGENT_ORCHESTRATION.md), [Context Management](./CONTEXT_MANAGEMENT.md), [Status Updates](./STATUS_UPDATES.md), [SITREP Format](./SITREP_FORMAT.md), [Cleanup Protocol](./CLEANUP_PROTOCOL.md)

---

## Purpose

This protocol defines the **behavioral discipline** that governs all skill execution. It draws from the obra/superpowers methodology (150K+ stars, battle-tested across thousands of projects) and adapts it to our skill architecture. Every skill references this standard.

The core insight: AI agents fail not because they lack capability, but because they rationalize skipping discipline. This protocol catches those rationalizations before they lead to skipped steps, false completion claims, or shallow fixes.

---

## The Five Steel Principles

Every skill must enforce these. They are non-negotiable.

### 1. NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE

```
IRON LAW: Claiming work is complete without verification is dishonesty, not efficiency.
```

**The Gate:** For every completion claim:
1. **IDENTIFY** the verification command (build, test, curl, browser check)
2. **RUN** it (don't assume it passes)
3. **READ** the output (don't skip it)
4. **VERIFY** the claim matches the output
5. **THEN** claim completion

**Forbidden phrases:**
- "Should work now"
- "I'm confident this fixes it"
- "This looks correct"
- "The changes seem right"
- "I believe this resolves the issue"
- "Based on my analysis, this should..."
- Expressing satisfaction before verification

**Required:** Show the evidence. "Build passes: `exit code 0`. Tests: `142/142 passing`. Verified in browser: screenshot attached."

### 2. NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST

```
IRON LAW: Symptom fixes create new bugs. Find the cause, then fix it.
```

- Collect evidence BEFORE forming hypotheses
- Test hypotheses with evidence, not assumptions
- Fix only the root cause, nothing more
- If 3 fix attempts fail, STOP - this is architectural

### 3. NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST (When TDD Applies)

```
IRON LAW: Write the test. Watch it fail. Write the code. Watch it pass. Refactor.
```

This applies to:
- `/test-ship` (always)
- `/subagent-dev` execution (always)
- `/investigate` regression tests (always)
- Other skills writing code (when test infrastructure exists)

This does NOT apply to:
- Config changes, documentation, planning, auditing
- Code changes where no test framework is configured
- Emergency hotfixes (but add the test immediately after)

### 4. NO IMPLEMENTATION BEFORE APPROVED DESIGN (When Planning Applies)

```
IRON LAW: Code without a plan is rework waiting to happen.
```

**The HARD-GATE:** When `/brainstorm` or `/mdmp` is active, NO implementation code may be written until:
- The design/spec is approved by the user
- The plan is written with specific, actionable steps
- The user selects a Course of Action

Skills where this applies: `/brainstorm`, `/mdmp`, `/subagent-dev` (requires a plan)
Skills where this does NOT apply: `/investigate` (evidence-first, not plan-first), `/incident` (urgency overrides planning)

### 5. NO PLACEHOLDERS IN PLANS OR SPECS

```
IRON LAW: Every step must contain actual code, exact file paths, exact commands.
```

**Forbidden in plans/specs:**
- "TBD", "TODO", "implement later"
- "Similar to Task N" (copy the actual content)
- "Add appropriate error handling" (specify the exact handling)
- "Update the tests" (specify which tests, what assertions)
- "Configure as needed" (specify the exact configuration)
- Empty function bodies with "// implement" comments
- References to "relevant files" (name them)

---

## Rationalization Defense

These are the most common excuses agents use to skip discipline. Each maps to a counter-argument. When you catch yourself thinking any of these, STOP - you're about to skip a critical step.

### Universal Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "This is too simple to need a plan" | Simple tasks with wrong assumptions create the worst bugs | At minimum, state the approach before coding |
| "I'll add the test after" | You won't. And without the test, you can't verify the fix works | Write the test first. RED-GREEN-REFACTOR. |
| "The user is waiting, I need to be fast" | A broken fast delivery wastes more time than a correct slower one | Quality is speed. Rework is slow. |
| "I already know what's wrong" | The most confident diagnoses produce the most embarrassing fixes | Verify with evidence. 60 seconds of checking saves 30 minutes of wrong-path work |
| "It's just a small change" | Small changes to the wrong thing break big things | Verify the change is correct before claiming completion |
| "The build was passing before, it'll pass now" | Run it. You'll be surprised how often this is wrong | `pnpm build && pnpm test` takes 30 seconds. Do it. |
| "I'll clean up the test data later" | Later never comes. Orphaned data causes the next debugging session | Clean up now. Always. |
| "This is an emergency, skip the process" | Emergencies are when process matters MOST - panic causes mistakes | Slow is smooth, smooth is fast |
| "I've verified similar code before" | This is DIFFERENT code. Verify THIS code. | Fresh evidence for every claim |
| "The user just wants it done" | The user wants it done RIGHT. A fix that breaks something else is worse than waiting | Take the extra minute to verify |
| "I'm running low on context" | That's a reason to be MORE careful, not less | Checkpoint state, delegate to sub-agent, or ask to continue in new session |
| "Three attempts is arbitrary" | No. After 3 attempts, you're pattern-matching, not reasoning. Fresh perspective needed | Escalate: upgrade tier, spawn fresh agent, or ask user |

### Skill-Specific Rationalization Defenses

Skills should add their own domain-specific rationalizations to this table. Examples:

**For /investigate:**
| "The stack trace points right to it" | Stack traces show WHERE, not WHY. The root cause may be upstream | Trace the data flow backward from the error |

**For /test-ship:**
| "This code is too tightly coupled to unit test" | That means it needs refactoring, not skipping | Write the test, let the pain guide the refactoring |

**For /sec-ship:**
| "This is internal-only, no one will exploit it" | Internal apps get breached. Employees make mistakes. Defense in depth. | Treat every endpoint as public |

---

## Red Flags (Self-Check)

If you notice yourself thinking any of these, you're about to violate discipline:

- "I'll just quickly..." (skipping verification)
- "Obviously this is..." (skipping investigation)
- "While I'm here, I'll also..." (scope creep)
- "This doesn't need a test because..." (test avoidance)
- "I'm pretty sure this will..." (assumption, not evidence)
- "Let me just try..." (guessing instead of investigating)
- "The user probably wants..." (assuming instead of asking)
- "I can fix this and that at the same time..." (multi-target fix = wrong root cause)

---

## Sub-Agent Discipline

When dispatching sub-agents (via Task tool), these rules apply:

### 1. Context Crafting

Sub-agents never inherit session context. The controller constructs exactly what each sub-agent needs:
- **Full task text** - not a reference to "Task 3 from the plan"
- **Scene-setting context** - what this code does, why it matters
- **Constraints** - what NOT to do, boundaries, scope lock
- **Expected output format** - exact structure for the return
- **Verification requirements** - what must be checked before claiming done

### 2. Model Selection

Choose the right model for the job:

| Task Type | Model | Examples |
|-----------|-------|---------|
| Literal pattern extraction | `haiku` | File listing, string matching, inventory |
| Code reasoning and modification | `sonnet` | Writing code, fixing bugs, analysis, reviews |
| Architecture and high-stakes decisions | `opus` | System design, complex debugging, external-facing reports |

**Decision rule:** If you're unsure between haiku and sonnet, use sonnet. The cost difference on Max is negligible. The quality difference is not.

### 3. Two-Stage Review (When Applicable)

For implementation tasks dispatched via `/subagent-dev`:

**Stage 1 - Spec Compliance:** "Did they build WHAT was requested?"
- Reviewer reads actual code, does NOT trust the implementer's report
- Checks: requirements met, nothing missing, no extra scope

**Stage 2 - Code Quality:** "Is what they built WELL-CONSTRUCTED?"
- Only runs AFTER Stage 1 passes
- Checks: clean code, tested, maintainable, no anti-patterns

### 4. Implementer Status Protocol

Sub-agents report one of four statuses:

| Status | Meaning | Controller Action |
|--------|---------|-------------------|
| `DONE` | Task complete, all checks pass | Proceed to review |
| `DONE_WITH_CONCERNS` | Complete but has questions/warnings | Review concerns, then proceed |
| `NEEDS_CONTEXT` | Missing information to complete | Provide context, re-dispatch |
| `BLOCKED` | Cannot complete due to blocker | Assess blocker, re-scope or escalate |

---

## Verification Protocol (Detailed)

This is the expanded version of Steel Principle #1, applicable across all skills.

### What to Verify and How

| Claim | Verification Method | Evidence Required |
|-------|-------------------|-------------------|
| "Build passes" | Run `pnpm build` (or equivalent) | Exit code 0, no warnings |
| "Tests pass" | Run `pnpm test` (or equivalent) | X/X passing, 0 failed |
| "Bug is fixed" | Reproduce original steps | Bug no longer occurs + screenshot/output |
| "Security issue resolved" | Re-run the specific scanner | Finding no longer appears |
| "Performance improved" | Re-run benchmark/Lighthouse | Before/after numbers |
| "Deployment succeeded" | Curl the endpoint or check dashboard | 200 OK, correct content |
| "Regression test written" | Run ONLY the new test against OLD code | Test FAILS (proves it catches the bug) |
| "No side effects" | Run full test suite | Same pass count as before, or better |

### Verification Applies to Sub-Agents Too

When a sub-agent claims DONE:
- **Do NOT trust the report** at face value
- Verify independently: check that the files were actually modified, tests actually pass, build actually succeeds
- The spec reviewer's job is literally "don't trust the implementer"

---

## The 3-Attempt Rule

Across all skills, when fixing issues:

| Attempt | Approach |
|---------|----------|
| 1 | Direct fix based on evidence |
| 2 | Alternative strategy (different approach to same root cause) |
| 3 | Conservative fallback (minimal change, safest option) |
| After 3 | **STOP.** Mark as BLOCKED. This is likely architectural, not a code bug. Escalate to user or flag for `/mdmp` review. |

**Never retry the same approach.** Each attempt must be meaningfully different.

---

## Scope Discipline

### Scope Lock

When working on a fix or implementation:
```
SCOPE LOCK: Changes restricted to [specific directory/files]
Any edit outside this scope requires explicit justification.
```

### No "While I'm Here" Changes

- Fix the bug. Don't refactor surrounding code.
- Implement the feature. Don't add extra configurability.
- Write the test. Don't restructure the test suite.
- Ship the PR. Don't clean up old branches.

Each of these is a separate task. If it matters, create a task for it. Don't bundle it.

### Blast Radius Check

| Files Changed | Action |
|--------------|--------|
| 1-3 files | Proceed normally |
| 4-5 files | Note in report, verify all tests pass |
| 6+ files | PAUSE. Multi-file changes often mean wrong root cause or scope creep. Confirm with user. |

---

## Announcement Pattern

When a skill activates, announce it clearly:

```
I'm using the /[skill-name] skill to [specific purpose].
Tier: [QUICK/STANDARD/DEEP or equivalent]
```

This tells the user what process is running and sets expectations for depth and duration.

---

## Reference in Skills

Add this to every skill file:

```markdown
## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:
- [Steel Principle most relevant to this skill]
- [Skill-specific rationalization defenses]
- [Verification requirements specific to this domain]
```

---

## Why This Matters

- **Without discipline:** Agent skips verification, claims "should work now," user discovers it doesn't. Agent guesses at root cause, applies wrong fix, creates new bug. Agent skips tests, regression appears in production.
- **With discipline:** Every claim is backed by evidence. Every fix traces to a root cause. Every test fails before it passes. Every plan is specific enough to execute. Zero rework, zero surprises.

The discipline protocol is what separates "it probably works" from "here's the proof it works."

---

**Adapted from:** obra/superpowers methodology (MIT License)
**Last Updated:** 2026-04-13
