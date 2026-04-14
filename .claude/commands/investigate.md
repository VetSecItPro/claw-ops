---
description: "/investigate — Systematic Root-Cause Debugging: evidence-first investigation, hypothesis testing, minimal fix, regression test"
allowed-tools: Bash(git *), Bash(npx *), Bash(npm *), Bash(pnpm *), Bash(yarn *), Bash(node *), Bash(cat *), Bash(ls *), Bash(find *), Bash(grep *), Bash(head *), Bash(tail *), Bash(wc *), Bash(date *), Bash(mkdir *), Bash(lsof *), Bash(curl *), Bash(jq *), Bash(kill *), Read, Write, Edit, Glob, Grep, Task, WebSearch, mcp__playwright__browser_navigate, mcp__playwright__browser_screenshot, mcp__playwright__browser_click, mcp__playwright__browser_type, mcp__playwright__browser_evaluate, mcp__playwright__browser_close, mcp__playwright__browser_wait_for, mcp__playwright__browser_snapshot, mcp__playwright__browser_press_key
---

# /investigate — Systematic Root-Cause Debugging

**The Steel Principle: No fixes without root cause investigation first.**

This skill enforces disciplined debugging. It prevents symptom-chasing, whack-a-mole fixes, and "I think it's this" guesswork. Every fix traces back to proven evidence. Every hypothesis is tested before action.

**FIRE AND FORGET** — Describe the bug. The skill investigates, diagnoses, fixes, tests, and reports.

> **When to use /investigate:**
> - Local dev bug you can't figure out
> - "It was working yesterday"
> - Flaky test or intermittent failure
> - Unexpected behavior (not a crash — just wrong)
> - Error message you don't understand
> - Something broke after a refactor
>
> **When NOT to use /investigate:**
> - Production is down → use `/incident` (production urgency + postmortem)
> - Security vulnerability → use `/sec-ship`
> - Performance is slow → use `/perf`
> - You know the fix but want it done → just ask directly

---

## COMPLEXITY TIERS

Like `/mdmp`, the skill auto-scales depth based on the bug's complexity:

| Tier | Criteria | Approach | Est. Time |
|------|----------|----------|-----------|
| **QUICK** | Single file, obvious stack trace, recent change | Read error → check git diff → fix → test | 1-3 min |
| **STANDARD** | Multi-file, unclear cause, no obvious recent change | Full 5-phase investigation, 3 hypotheses max | 5-15 min |
| **DEEP** | Architectural, intermittent, race condition, or 3 strikes on STANDARD | Rubber duck protocol, git bisect, dependency analysis, outside voice | 15-30 min |

**Auto-classification:** Based on initial evidence gathering:
- Single file in stack trace + recent git change touches that file → QUICK
- Multiple files or no clear stack trace → STANDARD
- Intermittent, timing-dependent, or "works on my machine" → DEEP
- Escalation: STANDARD auto-upgrades to DEEP after 3 failed hypotheses

---

## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:

- **Steel Principle #2:** NO fixes without root cause investigation first. This skill IS the enforcement of that law.
- **Steel Principle #1:** NO "fix verified" claims without re-running the reproduction steps and observing the bug is gone
- **3-strike rule:** After 3 failed hypotheses at STANDARD tier, escalate. Do not keep guessing.

### /investigate-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The stack trace points right to it, I know the cause" | Stack traces show WHERE, not WHY. The root cause may be 3 stack frames upstream. | Trace the data flow backward. Verify with evidence. |
| "I've seen this pattern before, I know the fix" | Same symptom + different cause = wrong fix | Collect evidence FROM THIS bug, not from memory |
| "This one is simple, skip the regression test" | The easiest bugs to miss are the ones you thought were simple | Every fix ships with a test that would fail without the fix. Non-negotiable. |
| "The fix touches 6 files but it's definitely right" | 6+ file fixes are almost always symptom-chasing | STOP. Reconsider. The root cause is probably upstream of all 6. |
| "I'll remove the temp logs later" | Temp logs ship to prod. Users see `[INV]` in their console. | Remove `[INV]` markers NOW, before claiming fix complete |
| "This looks architectural but one more try..." | 4th attempt on a 3-strike bug is just denial | Escalate. Upgrade to DEEP tier. Spawn outside voice. Or ask user. |

---

## CRITICAL RULES

1. **IRON LAW** — Never apply a fix without confirmed root cause. Symptom fixes create new bugs.
2. **EVIDENCE CHAIN** — Every hypothesis MUST link to specific evidence. No "I think" — only "the log at file:line shows X".
3. **3-STRIKE RULE** — After 3 failed hypotheses at STANDARD tier, STOP. Escalate to DEEP tier or ask the user. Don't keep guessing.
4. **SCOPE LOCK** — During investigation, restrict edits to the narrowest affected directory. No "while I'm here" improvements.
5. **MINIMAL FIX** — Fix the root cause, nothing more. No refactoring, no cleanup, no "improvements" to surrounding code.
6. **REGRESSION TEST MANDATORY** — Every fix ships with a test that would fail without the fix.
7. **BLAST RADIUS CHECK** — If fix touches >5 files, pause and confirm with user. Multi-file fixes often mean wrong root cause.
8. **NEVER GUESS AT ARCHITECTURE** — If you don't understand how the system works, read the code first. Ask the user if needed.

---

## STATUS UPDATES

This skill follows the **[Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)**.

```
🔍 /investigate Started
   Bug: "[description]"
   Tier: STANDARD (multi-file, no obvious cause)

📋 Phase 1: Evidence Collection
   ├─ Stack trace parsed → 3 files identified
   ├─ Git log: 4 commits in last 24h touch affected area
   ├─ Error reproduced: YES (consistent on page reload)
   └─ ✅ Evidence collected — 6 data points

🧩 Phase 2: Pattern Analysis
   ├─ Matches: State corruption pattern (stale closure)
   └─ ✅ 1 pattern match, 0 prior learnings

🔬 Phase 3: Hypothesis Testing
   ├─ H1: Stale closure in useEffect → ❌ DISPROVED (cleanup exists)
   ├─ H2: Race condition in parallel fetch → ✅ CONFIRMED
   │       Evidence: console.log shows response B arriving before A
   └─ ✅ Root cause confirmed on hypothesis 2

🔧 Phase 4: Fix Implementation
   ├─ Fix: Added AbortController + sequential await
   ├─ Files modified: 1 (src/hooks/useFetch.ts)
   ├─ Build: ✅ Pass
   ├─ Tests: ✅ 142/142 passing
   └─ Regression test: ✅ Written (useFetch.race.test.ts)

✅ Phase 5: Verified
   Duration: 8 minutes
   Root Cause: Race condition — parallel fetches with no abort on unmount
   Fix: AbortController + sequential execution for dependent fetches
   Confidence: 9/10
```

---

## CONTEXT MANAGEMENT

This skill follows the **[Context Management Protocol](~/.claude/standards/CONTEXT_MANAGEMENT.md)**.

Key rules for this skill:
- Investigation agents return < 500 tokens (full evidence written to `.investigate/`)
- State file `.investigate/state-YYYYMMDD-HHMMSS.json` tracks phase, hypotheses, evidence
- Resume from checkpoint if context resets
- QUICK tier: no sub-agents, do everything inline
- STANDARD tier: max 1 sub-agent for parallel evidence gathering
- DEEP tier: up to 2 sub-agents (git bisect + dependency analysis can run in parallel)
- Fix agents run sequentially (they modify code)

---

## OUTPUT STRUCTURE

```
.investigate/
├── INV-YYYYMMDD-HHMMSS.md        # Debug report (living document)
├── state-YYYYMMDD-HHMMSS.json    # Checkpoint state
├── patterns.json                  # Learned patterns (persists across sessions)
├── evidence/                      # Evidence files
│   ├── stack-trace.txt            # Parsed stack trace
│   ├── git-diff.txt               # Recent changes in affected area
│   ├── reproduction-steps.md      # How to reproduce
│   └── screenshots/               # Browser screenshots (frontend bugs)
└── .gitignore entry               # .investigate/ must be gitignored
```

---

## PHASE 0: INTAKE & CLASSIFICATION

### 0.1 Parse the Bug Report

Extract from user input:
- **Symptom:** What's happening that shouldn't be?
- **Expected behavior:** What should happen instead?
- **Reproduction:** Steps to trigger (if known)
- **Context:** When did it start? What changed recently?
- **Error message / stack trace:** If any

### 0.2 Stack Trace Parser + Source Mapper

If an error message or stack trace is provided:

1. **Parse the stack trace** — extract file paths, line numbers, function names
2. **Source map resolution** — if the trace references compiled/bundled code, map back to source
3. **Auto-read the relevant files** — read each file at the referenced line ±20 lines
4. **Build a call chain** — ordered list of functions from trigger to error

```
Call Chain (from stack trace):
  1. src/hooks/useFetch.ts:45    → fetchData()
  2. src/lib/api-client.ts:23    → apiClient.get()
  3. src/app/api/users/route.ts:12 → GET handler
  Error: TypeError: Cannot read property 'id' of undefined
```

This auto-reading gives immediate context without manual file hunting.

### 0.3 Classify Complexity

Based on initial evidence:

| Signal | Tier |
|--------|------|
| Single file in stack trace + recent git change | QUICK |
| Clear error message + reproducible | QUICK |
| Multiple files, no clear stack trace | STANDARD |
| "It works sometimes" / "only in production" / "after a while" | DEEP |
| No error message, just wrong behavior | STANDARD |
| Race condition or timing-dependent | DEEP |

### 0.4 Create Report Infrastructure

```bash
mkdir -p .investigate/evidence/screenshots
# Create report skeleton
# Create state file
# Ensure .investigate/ is in .gitignore
```

---

## PHASE 1: EVIDENCE COLLECTION

**Purpose:** Gather ALL available evidence BEFORE forming any hypothesis. Resist the urge to jump to conclusions.

### 1.1 Error & Log Evidence

- Full error message and stack trace (if any)
- Console output (errors, warnings)
- Network errors (if applicable)
- Server logs (if accessible)

### 1.2 Git Evidence (Diff-Aware Investigation)

**This is the highest-value evidence source.** Most bugs are in recent changes.

```bash
# What changed in the last 24 hours in affected files?
git log --oneline --since="24 hours ago" -- [affected files/dirs]

# What changed on this branch vs main?
git diff main...HEAD -- [affected files/dirs]

# Who last touched the affected files?
git log -3 --format="%h %an %s" -- [affected file]
```

**Diff-aware shortcut:** If a recent commit touches the exact file:line from the stack trace, the cause is almost certainly in that diff. Read the diff first.

### 1.3 Dependency Evidence (STANDARD+)

```bash
# Did any packages change recently?
git diff main...HEAD -- package.json pnpm-lock.yaml yarn.lock package-lock.json

# Are there any known issues with recently updated packages?
# Check changelogs for breaking changes
```

### 1.4 Browser Evidence (Frontend Bugs)

If the bug is in the UI:

1. Navigate to the affected page via Playwright MCP
2. Take a screenshot → `.investigate/evidence/screenshots/symptom.png`
3. Capture console errors via `browser_evaluate`:
   ```javascript
   window.__investigate_errors = [];
   const orig = console.error;
   console.error = (...args) => {
     window.__investigate_errors.push(args.join(' '));
     orig(...args);
   };
   ```
4. Capture network failures
5. Attempt reproduction — document exact steps

### 1.5 Reproduction Attempt

- Try to reproduce the bug consistently
- If reproducible: document exact steps in `.investigate/evidence/reproduction-steps.md`
- If intermittent: note frequency and conditions (DEEP tier likely)
- If cannot reproduce: note what was tried, escalate to DEEP

### 1.6 Prior Learnings Check

Read `.investigate/patterns.json` (if exists) for known patterns in this codebase:
- Has this file had bugs before? (recurrence signal)
- Are there known pitfalls in this area?
- Did a prior investigation leave notes about this module?

### 1.7 Evidence Summary

```
═══════════════════════════════════════════════════════════════════
📋 EVIDENCE COLLECTED
═══════════════════════════════════════════════════════════════════

**Data Points:** [X]

| # | Evidence Type | Finding |
|---|-------------|---------|
| E1 | Stack trace | TypeError at src/hooks/useFetch.ts:45 |
| E2 | Git diff | commit abc123 (2h ago) modified useFetch.ts |
| E3 | Console | 2 errors: "Cannot read property 'id' of undefined" |
| E4 | Reproduction | Consistent on page reload after navigation |
| E5 | Dependencies | No package changes in last 7 days |
| E6 | Prior patterns | useFetch.ts had a race condition fix 3 months ago |

───────────────────────────────────────────────────────────────────
```

**QUICK tier:** If evidence clearly points to one cause (e.g., E2 shows the exact line changed), skip Phase 2 and go directly to Phase 3 with a single hypothesis.

---

## PHASE 2: PATTERN ANALYSIS

**Purpose:** Match the bug signature against known patterns. Don't reinvent the wheel.

### Known Bug Pattern Library

| # | Pattern | Signature | Common Cause |
|---|---------|-----------|-------------|
| P1 | **Stale Closure** | State value is old inside useEffect/callback | Missing dependency in useEffect deps array |
| P2 | **Race Condition** | Results appear in wrong order, intermittent wrong data | Parallel async without abort/cancellation |
| P3 | **Nil Propagation** | "Cannot read property X of undefined/null" | Missing null check after async fetch or optional chain |
| P4 | **State Corruption** | UI shows stale or mixed data | Shared mutable state, missing deep clone, or context bleed |
| P5 | **Hydration Mismatch** | "Text content did not match" / flicker on load | Server/client render different output (Date, Math.random, window) |
| P6 | **Import Cycle** | Module is undefined at runtime despite being exported | Circular dependency between modules |
| P7 | **Configuration Drift** | "Works in dev, breaks in prod" | Env vars missing, different Node version, different build config |
| P8 | **Event Handler Leak** | Memory grows over time, handlers fire multiple times | addEventListener without removeEventListener in cleanup |
| P9 | **Async State Update** | "Can't perform React state update on unmounted component" | Async operation completes after component unmounts |
| P10 | **Type Mismatch** | Subtle wrong behavior (no crash) | String "1" vs number 1, undefined vs null, truthy/falsy edge |
| P11 | **Cache Staleness** | Old data shows after mutation | Cache not invalidated after write, SWR/React Query not revalidated |
| P12 | **Migration Drift** | DB query fails with column not found | Code references column that was renamed/removed in migration |
| P13 | **Build Artifact** | Works in dev, fails in production build | Tree-shaking removed "unused" code, or SSR/CSR boundary issue |
| P14 | **Timing Window** | Bug only appears under load or slow network | Missing loading state, optimistic update race, or debounce gap |
| P15 | **Silent Swallow** | No error but wrong behavior | try/catch with empty catch block hiding the real error |

**Match evidence against patterns.** If a pattern matches with high confidence, proceed directly to hypothesis testing with that pattern as H1.

### Web Search (if no pattern match)

If the error message is specific and no pattern matches:
```
Search: "[exact error message]" + [framework name]
```
Check top 3 results for known issues, GitHub issues, or Stack Overflow solutions.

---

## PHASE 3: HYPOTHESIS TESTING

**Purpose:** Test each hypothesis with EVIDENCE, not assumptions. Maximum 3 hypotheses before escalation.

### Hypothesis Format

For each hypothesis:

```
═══════════════════════════════════════════════════════════════════
🔬 HYPOTHESIS [N]/3
═══════════════════════════════════════════════════════════════════

**Claim:** [What you think is causing the bug]
**Based on:** [Evidence IDs: E1, E3, E6]
**Pattern match:** [P2 (Race Condition) — or "None"]
**Test:** [How to verify — specific action, not "check if it works"]
**Prediction:** [If this hypothesis is correct, then [specific observable result]]

**Result:** ✅ CONFIRMED / ❌ DISPROVED
**Evidence:** [What you observed that confirms or disproves]
═══════════════════════════════════════════════════════════════════
```

### Testing Methods

| Method | When to Use | How |
|--------|------------|-----|
| **Temporary log** | Need to see a value at runtime | Add `console.log('[INV]', variable)`, reproduce, check output |
| **Assertion** | Need to verify a condition holds | Add `if (!condition) throw new Error('[INV] condition failed')` |
| **Breakpoint trace** | Need to follow execution flow | Add logs at each step of the flow |
| **Isolation test** | Need to rule out external factors | Call the function directly with known inputs |
| **Git revert test** | Need to confirm a specific commit caused it | `git stash && git checkout [suspect commit~1]` — does bug disappear? |
| **Dependency swap** | Need to check if a package update caused it | Temporarily revert the package version |

**IMPORTANT:** Remove ALL temporary logs/assertions after testing. They are evidence-gathering tools, not permanent code.

### 3-Strike Rule

After 3 failed hypotheses:

```
⚠️ 3 STRIKES — ESCALATION REQUIRED
═══════════════════════════════════════════════════════════════════

All 3 hypotheses disproved. Options:

A) **Upgrade to DEEP tier** — git bisect, dependency analysis,
   rubber duck protocol, outside voice
B) **Add instrumentation** — add targeted logging to the affected
   code path and wait for the bug to recur with more data
C) **Escalate to user** — present findings so far, ask for
   additional context or constraints
D) **Architectural concern** — the bug may indicate a design flaw,
   not a code bug. Flag for /mdmp review.
═══════════════════════════════════════════════════════════════════
```

---

## PHASE 3.5: DEEP INVESTIGATION (DEEP tier only)

**Activated when:** Bug classified as DEEP from the start, or escalated after 3 strikes.

### 3.5a Rubber Duck Protocol

Before forming more hypotheses, force yourself to explain the system:

1. **What is this code supposed to do?** (Expected behavior, step by step)
2. **What data flows through it?** (Input → transformations → output)
3. **What are the preconditions?** (What must be true for this to work?)
4. **Which precondition is violated?** (This is often the root cause)

Write the answers to `.investigate/evidence/rubber-duck.md`. The act of explaining often reveals the bug.

### 3.5b Git Bisect Automation

Find the exact commit that introduced the bug:

```bash
# Start bisect
git bisect start
git bisect bad HEAD
git bisect good [last known good commit or tag]

# For each step: reproduce the bug
# git bisect good / git bisect bad based on result
# Continue until the offending commit is found
```

**Automation:** If the bug has a reproducible test:
```bash
git bisect run [test command that exits 0 if bug is absent, 1 if present]
```

### 3.5c Dependency Diff Analysis

```bash
# Compare lockfile changes between good and bad states
git diff [good commit]..[bad commit] -- pnpm-lock.yaml | head -100

# Check if any dependency had a breaking change
# Read changelog for each changed package
```

### 3.5d Outside Voice (Fresh Perspective)

Spawn a fresh-context Agent subagent with:
- The bug symptom
- The evidence collected so far
- The 3 disproved hypotheses
- The rubber duck analysis

Ask: "Given this evidence and these failed hypotheses, what would you investigate next?"

This catches blind spots — the investigator may be too close to the code to see the obvious.

---

## PHASE 4: FIX IMPLEMENTATION

**Purpose:** Fix the ROOT CAUSE, not the symptom. Minimal diff. Regression test mandatory.

### 4.1 Scope Lock

Before making any changes, declare the scope boundary:
```
SCOPE LOCK: Changes restricted to [directory/file]
Any edit outside this scope requires explicit justification.
```

### 4.2 Apply the Fix

1. **Fix only the root cause** — no cleanup, no refactoring, no "while I'm here"
2. **Add a WHY comment** if the fix is non-obvious:
   ```typescript
   // Fix for INV-20260331: Race condition — abort previous fetch
   // when a new one starts. Without this, stale responses overwrite
   // fresh data when navigation is faster than the API.
   const controller = new AbortController();
   ```
3. **Keep the diff minimal** — if the fix touches >5 files, pause and reconsider

### 4.3 Blast Radius Check

| Files Changed | Action |
|--------------|--------|
| 1 file | Proceed |
| 2-5 files | Proceed with note in report |
| 6+ files | STOP — ask user. Multi-file fixes often mean wrong root cause. |

### 4.4 Build & Test Verification

```bash
# Build must pass
pnpm build  # or equivalent

# Existing tests must pass
pnpm test:run  # or equivalent

# If either fails: revert the fix and reassess
```

### 4.5 Regression Test (MANDATORY)

Write a test that:
- **FAILS** without the fix (reproduces the bug)
- **PASSES** with the fix (proves the fix works)
- Tests the ROOT CAUSE, not the symptom

```
Test location: __tests__/investigate/ or colocated with the fixed file
Test name: it('INV-YYYYMMDD: [bug description]', ...)
Comment: // Regression test for INV-YYYYMMDD — [root cause description]
```

If the bug is in UI behavior that's hard to unit test, write an integration test or document manual reproduction steps in the report.

---

## PHASE 5: VERIFICATION & REPORT

### 5.1 Reproduce Original Scenario

After the fix:
1. Follow the exact reproduction steps from Phase 1
2. Verify the bug no longer occurs
3. If frontend: take an "after" screenshot → `.investigate/evidence/screenshots/fixed.png`

### 5.2 Full Test Suite

```bash
pnpm test:run  # All tests pass
pnpm build     # Build succeeds
```

### 5.3 Pattern Learning

If this investigation revealed a non-obvious pattern, save it:

```json
// .investigate/patterns.json (append)
{
  "patterns": [
    {
      "id": "PROJ-001",
      "date": "2026-03-31",
      "area": "src/hooks/",
      "pattern": "useFetch hooks need AbortController for dependent fetches",
      "symptom": "Stale data after rapid navigation",
      "root_cause": "Race condition — slow response overwrites fast response",
      "confidence": 9,
      "source": "INV-20260331-143022"
    }
  ]
}
```

These patterns are checked in Phase 1.6 of future investigations.

### 5.4 Debug Report

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md) — use the unified template with domain-specific additions below.

```markdown
# Investigation Report: INV-YYYYMMDD-HHMMSS

**Bug:** [symptom description]
**Tier:** [QUICK / STANDARD / DEEP]
**Duration:** [X minutes]
**Status:** ✅ RESOLVED / ⚠️ MITIGATED / ❌ UNRESOLVED

---

## Root Cause

**What:** [One sentence — the actual cause]
**Why:** [Why this happened — what made it possible]
**Where:** [file:line]
**Confidence:** [1-10]

---

## Evidence Chain

| # | Evidence | Linked to Hypothesis |
|---|---------|---------------------|
| E1 | [evidence] | H2 (confirmed) |
| E2 | [evidence] | H1 (disproved) |

---

## Hypothesis Log

| # | Hypothesis | Result | Evidence |
|---|-----------|--------|---------|
| H1 | [claim] | ❌ Disproved | [why] |
| H2 | [claim] | ✅ Confirmed | [proof] |

---

## Fix

**Files Modified:** [count]
**Diff:** [summary of changes]
**Regression Test:** [test file:line]

---

## Pattern Learned

[If applicable — saved to patterns.json for future investigations]

---

## Handoff

- [ ] Run `/gh-ship` to commit the fix
- [ ] If production impact suspected → run `/incident` for postmortem
- [ ] If architectural concern flagged → run `/mdmp` for design review
```

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Investigate-Specific Cleanup

1. **Remove ALL temporary logs** — grep for `[INV]` markers and remove
2. **Close Playwright browser** if opened for frontend investigation
3. **End git bisect** if started: `git bisect reset`
4. **Gitignore enforcement** — ensure `.investigate/` is in `.gitignore`
5. **Keep evidence** — screenshots, reproduction steps, and patterns persist for future reference
6. **Verify working tree clean** — no stashed changes or detached HEAD from bisect

---

## RELATED SKILLS

**Feeds from:**
- `/incident` - production incidents often spawn an investigation for root cause; incident triage hands off to investigate for the deep dive
- `/monitor` - degraded health scores or runtime error spikes from monitor are investigation triggers
- `/qatest` - QA findings that can't be auto-fixed escalate to investigate for root cause

**Feeds into:**
- `/gh-ship` - once the fix is confirmed, ship it with gh-ship
- `/incident` - if the bug turns out to be production-affecting, escalate to incident for postmortem
- `/sec-ship` - if investigation reveals a security issue, hand off to sec-ship for the full security pipeline
- `/mdmp` - if investigation reveals an architectural concern (not just a code bug), flag for design review

**Pairs with:**
- `/browse` - browser evidence gathering for frontend bugs
- `/smoketest` - run smoketest after the fix to confirm nothing else broke

**Auto-suggest after completion:**
When fix is confirmed and tests pass, suggest: `/gh-ship` to commit and ship the fix; if the bug had production impact, also suggest `/incident` for a postmortem

---

## REMEMBER

- **Steel Principle** — No fix without confirmed root cause. Period.
- **Evidence before hypothesis** — Collect data first, theorize second
- **3 strikes then escalate** — Don't keep guessing. Upgrade tier or ask for help.
- **Minimal fix** — Fix the root cause, nothing else. No cleanup, no refactoring.
- **Regression test is mandatory** — The same bug should never happen twice
- **Scope lock** — Stay in the affected area. Wandering creates new bugs.
- **Git is your best witness** — `git diff`, `git log`, `git bisect` reveal most bugs
- **Patterns compound** — Save what you learn. Future investigations start faster.
- **Browser bugs need browser evidence** — Screenshots, console errors, network failures
- **Intermittent means DEEP** — Timing bugs deserve the full treatment

<!-- Claude Code Skill by Steel Motion LLC — https://steelmotion.dev -->
<!-- Part of the Claude Code Skills Collection -->
<!-- Powered by Claude models: Haiku (fast extraction), Sonnet (balanced reasoning), Opus (deep analysis) -->
<!-- License: MIT -->
<!-- Incorporates debugging methodology concepts from gstack /investigate by Garry Tan (MIT) -->
