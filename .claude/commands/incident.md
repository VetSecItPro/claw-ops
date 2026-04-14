---
description: "/incident -- Structured Incident Response: triage production issues, identify root cause, fix, verify, and write postmortem"
allowed-tools: Bash(cat:*), Bash(ls:*), Bash(find:*), Bash(wc:*), Bash(curl:*), Bash(dig:*), Bash(openssl:*), Bash(node:*), Bash(npx:*), Bash(npm:*), Bash(bun:*), Bash(pnpm:*), Bash(yarn:*), Bash(git:*), Bash(jq:*), Bash(date:*), Bash(mkdir:*), Bash(touch:*), Bash(head:*), Bash(tail:*), Bash(grep:*), Bash(sort:*), Bash(uniq:*), Bash(gh:*), Read, Write, Edit, Glob, Grep, Task, WebFetch, WebSearch, mcp__vercel__list_deployments, mcp__vercel__get_deployment, mcp__vercel__get_deployment_build_logs, mcp__vercel__get_runtime_logs, mcp__vercel__list_projects, mcp__vercel__get_project, mcp__vercel__web_fetch_vercel_url
---

# /incident — Structured Incident Response

**Purpose:** When production breaks, provide military-grade structure: assess the situation, identify root cause, fix it, verify the fix, write the postmortem. Every step documented, every action timestamped.

**Philosophy:**
1. Structured triage, not panic — follow the process, even under pressure
2. Evidence-first — collect logs and data before forming hypotheses
3. Fix the right thing — a wrong root cause wastes all subsequent effort
4. Document everything — the postmortem is not optional, even for minor incidents
5. Prevent recurrence — every incident results in at least one regression test

<!--
## Design Rationale

### When to use /incident
- Production is broken: 500 errors, white screens, data not loading
- Performance has degraded significantly (>3x slower than baseline)
- Users are reporting issues you can reproduce on production
- /monitor returned a health score < 60 (F grade)
- After a deploy introduced a regression
- Security incident detected (then also run /sec-ship after resolution)

### When NOT to use /incident
- Local development bugs (just debug normally)
- Pre-deploy testing issues (use /qatest)
- Performance optimization (use /perf — incident is for breaks, not slowness)
- Planned maintenance or migrations (use /migrate)
- Feature requests or improvements (normal development)

### Flow
```
  /incident "500 errors on /api/auth"
     │
     ▼
  ┌──────────────────────────────┐
  │  Stage 0: Initialization     │─── Parse description, create report, set severity
  │  Stage 1: Triage             │─── Blast radius: what's broken, since when, how many users
  │  Stage 2: Evidence           │─── Logs, errors, recent deploys, recent commits
  │  Stage 3: Root Cause         │─── Correlate evidence, identify the breaking change [OPUS]
  │  Stage 4: Fix Development    │─── Fix → Build → Test → Verify (iterative loop)
  │  Stage 5: Fix Verification   │─── Verify fix resolves issue, no regressions
  │  Stage 6: Deploy Verification│─── Check production after deploy (delegates to /monitor)
  │  Stage 7: Regression Tests   │─── Add test cases that catch this incident type
  │  Stage 8: Postmortem         │─── Timeline, root cause, action items
  └──────────────────────────────┘
     │
     ▼
  .incident-reports/INC-YYYYMMDD-HHMMSS.md
  .incident-reports/INC-YYYYMMDD-HHMMSS-postmortem.md
```

### Key Design Decisions
1. Root cause analysis uses opus — this is the one place where deep reasoning matters most
2. Fix loop mirrors sec-ship Stage 4 pattern: FOUND -> FIXING -> FIXED/DEFERRED/BLOCKED
3. Every action is timestamped for accurate postmortem timeline
4. Regression tests are mandatory — the same bug should never happen twice
5. Postmortem is not optional — even SEV-4 incidents get documented
6. Severity classification drives response urgency, not thoroughness
-->

---

## DISCIPLINE

> Reference: [Superpowers Discipline Protocol](~/.claude/standards/STEEL_DISCIPLINE.md)

Key enforcements for this skill:
- **Steel Principle #1:** NO completion claims without fresh verification evidence — the fix must be validated against real production traffic/logs
- **Steel Principle #2:** NO hypothesis-driven fixes; collect evidence FIRST, then form root cause
- Regression test is non-negotiable — no closed incident without a test that would catch it

### Incident-Specific Rationalization Table

| Rationalization | Reality | What to Do |
|----------------|---------|------------|
| "The deploy succeeded, no need to monitor" | Deploy success != working in production; user-visible breakage happens post-deploy | Run /monitor post-fix; verify real requests succeed |
| "Looks healthy now, skip the detailed checks" | Surface health hides deep issues (error rate, latency tails, cold starts) | Full health suite before declaring resolved |
| "This is obviously a caching issue" | Jumping to root cause wastes time when evidence points elsewhere | Collect evidence first; correlate before concluding |
| "SEV-4 doesn't need a postmortem" | All incidents teach; postmortem discipline catches pattern bugs | Every incident gets a postmortem, length proportional to severity |

---

## STATUS UPDATES

> Reference: [Status Update Protocol](~/.claude/standards/STATUS_UPDATES.md)

Provide status updates every 15-30 seconds during active triage and fix. Incidents are time-sensitive — more frequent updates than normal skills.

### Incident-Specific Status Examples

```
🚨 Stage 0/8 — Incident Initialized
   ├─ Description: "500 errors on /api/auth"
   ├─ Severity: SEV-2 (High)
   └─ Incident ID: INC-20260224-143022

🔍 Stage 1/8 — Triage
   ├─ Blast radius: /api/auth, /api/auth/callback, /login
   ├─ 3 routes affected, first error: 14:22 UTC
   ├─ Error rate: ~15 errors/min (from runtime logs)
   └─ Assessing user impact...

🔬 Stage 3/8 — Root Cause Analysis [opus]
   ├─ Hypothesis: Auth middleware change in commit abc1234
   ├─ Correlation: errors started 3 min after deploy #847
   ├─ Confidence: HIGH
   └─ Root cause: Missing await on async session check

🔧 Stage 4/8 — Fix Development [attempt 1/3]
   ├─ Applying fix to src/middleware.ts:42
   ├─ Building...
   ├─ Build passed ✅
   ├─ Running tests...
   └─ Tests passed ✅ (32/32)

✅ Stage 8/8 — Postmortem Complete
   ├─ Total incident duration: 18 minutes
   ├─ Root cause: Missing await in auth middleware
   ├─ Fix: 1 file changed, 1 line modified
   ├─ Regression test added: auth-middleware.test.ts
   └─ Report: .incident-reports/INC-20260224-143022.md
```

---

## CONTEXT MANAGEMENT

> Reference: [Context Management Protocol](~/.claude/standards/CONTEXT_MANAGEMENT.md)

### Incident-Specific Context Rules

1. **Sub-agents return < 500 tokens.** Evidence collection writes full logs to `.incident-reports/evidence/`.
2. **State file:** `.incident-reports/state-YYYYMMDD-HHMMSS.json` — updated after every stage.
3. **Resume protocol:** Check for incomplete incidents from last 2 hours before starting fresh.
4. **Evidence agents can run in parallel (max 2).** Fix agents are ALWAYS sequential.
5. **Orchestrator never reads full log dumps** — only agent summaries.
6. **Checkpoint after every stage** — incidents can be long; context protection is critical.
7. **Timeline entries are append-only** — never modify past timeline entries, only add new ones.

---

## AGENT ORCHESTRATION

> Reference: [Agent Orchestration Protocol](~/.claude/standards/AGENT_ORCHESTRATION.md)

### Model Selection Table

| Agent | Model | Rationale |
|-------|-------|-----------|
| Triage (blast radius) | sonnet | Pattern matching across routes and logs |
| Triage (user impact) | sonnet | Analyze error rates and affected endpoints |
| Evidence: Logs | sonnet | Parse and summarize runtime logs |
| Evidence: Git History | sonnet | Correlate commits with error timeline |
| Root Cause Analysis | **opus** | Deep reasoning on complex evidence — wrong root cause wastes everything |
| Fix Development | sonnet | Code modification with build verification |
| Fix Verification | sonnet | Regression testing and verification |
| Deploy Verification | sonnet | Production health check (or delegate to /monitor) |
| Regression Test Writing | sonnet | Generate test cases from incident evidence |

### Why Opus for Root Cause

Root cause analysis is the critical decision point of any incident. A wrong root cause leads to:
- Wasted time fixing the wrong thing
- The real issue persisting or worsening
- False confidence that the incident is resolved

Opus provides the deep reasoning needed to:
- Correlate multiple evidence streams (logs + git history + deployment timeline)
- Distinguish correlation from causation
- Identify subtle issues (race conditions, async errors, configuration drift)

### Agent Batching Rules

| Incident Stage | Parallel Agents | Sequential Agents |
|---------------|----------------|-------------------|
| Triage | 2 (blast radius + user impact) | — |
| Evidence | 2 (logs + git history) | — |
| Root Cause | — | 1 opus (needs all evidence) |
| Fix | — | 1 per attempt (build-verify loop) |
| Verification | — | 1 (must verify sequentially) |
| Regression Tests | — | 1 (needs fix context) |

---

## REPORT PERSISTENCE

### Finding Lifecycle

Every incident finding follows this lifecycle:

```
FOUND → TRIAGING → FIXING → FIXED | DEFERRED | BLOCKED
```

- **FOUND**: Issue detected or reported
- **TRIAGING**: Actively investigating blast radius and root cause
- **FIXING**: Fix in progress (attempt N of 3)
- **FIXED**: Fix applied, verified, and deployed
- **DEFERRED**: Cannot fix now (needs architectural change, external dependency, etc.)
- **BLOCKED**: 3 fix attempts failed, needs human intervention

### Fix Attempt Tracking

Each fix attempt is tracked:

```markdown
#### Fix Attempt 1/3
- **Hypothesis:** Missing await on async session check
- **Change:** Added await to line 42 of src/middleware.ts
- **Build:** ✅ Passed
- **Tests:** ✅ 32/32 passed
- **Verification:** ✅ /api/auth returns 200
- **Result:** FIXED
```

If attempt fails:
```markdown
#### Fix Attempt 1/3
- **Hypothesis:** Missing await on async session check
- **Change:** Added await to line 42 of src/middleware.ts
- **Build:** ✅ Passed
- **Tests:** ❌ 2/32 failed (auth-callback.test.ts)
- **Result:** REVERTED — Tests failed, reverting change
- **Next:** Re-analyze with additional test failure context
```

### Persistence Rules

1. Report file created at Stage 0, updated after every stage and every fix attempt
2. State file updated after every stage with machine-readable status
3. Timeline entries are APPEND-ONLY — never modify past entries
4. Evidence is preserved in `evidence/` directory for postmortem
5. Fix attempts are logged with full context (hypothesis, change, result)
6. Previous incident reports are checked for recurring patterns

### Resume Protocol

Before starting a new incident:
1. Check for `.incident-reports/state-*.json` from last 2 hours with `status: "in_progress"`
2. If found, ask user: "Resume incident INC-YYYYMMDD-HHMMSS or start new?"
3. If resuming, load existing findings, evidence, and timeline
4. If starting fresh, archive previous state file

---

## SEVERITY CLASSIFICATION

| Severity | Criteria | Response Priority | Fix Approach |
|----------|----------|------------------|--------------|
| **SEV-1 (Critical)** | Full outage, data loss risk, security breach | Immediate. Fix-first, document-after | Skip to Stage 4 if root cause is obvious |
| **SEV-2 (High)** | Major feature broken, significant user impact | Fast triage, structured fix | Full pipeline, expedited |
| **SEV-3 (Medium)** | Degraded performance, minor feature broken | Standard workflow | Full pipeline, full documentation |
| **SEV-4 (Low)** | Cosmetic issue, edge case, non-blocking | Can queue. Full analysis still required | Full pipeline, may defer fix |

### Severity Auto-Detection

Automatically assess severity from the incident description and initial triage:

- **500 errors on auth/login/payment endpoints** → SEV-1 (user-blocking)
- **500 errors on non-critical endpoints** → SEV-2
- **Elevated error rates but no outage** → SEV-3
- **Intermittent errors, edge cases** → SEV-3 or SEV-4
- **Cosmetic/UI issues** → SEV-4
- **User reports "site is down"** → SEV-1 until proven otherwise

The user can override severity at any time.

---

## HUMAN DECISION TRIGGERS

**Pause and ask the user before proceeding if:**

1. **Root cause is ambiguous** — multiple equally likely hypotheses, need human judgment
2. **Fix requires architectural change** — not a quick fix, needs design discussion
3. **Fix might break other features** — blast radius of the fix is uncertain
4. **3 fix attempts have failed** — escalate, don't keep trying blindly
5. **Incident involves data corruption** — any data mutation needs explicit approval
6. **Incident involves security** — potential security breach needs immediate human awareness
7. **Root cause is in a dependency** — can't fix in this codebase, needs upstream action
8. **Severity escalation** — triage reveals the incident is worse than initially described

---

## RUN MODES

```
/incident "500 errors on /api/auth"          # Full incident response (default)
/incident --triage-only "checkout is slow"    # Triage + root cause only, no auto-fix
/incident --postmortem INC-20260224-143022    # Generate postmortem from existing incident report
/incident --retest INC-20260224-143022        # Re-verify a previous incident's fix
```

### Mode Behavior

| Mode | Stages Run | Use Case |
|------|-----------|----------|
| Full (default) | 0-8 | Production is broken, fix it end-to-end |
| `--triage-only` | 0-3 | Understand the problem, decide manually whether to fix |
| `--postmortem` | 8 only | Generate postmortem from completed incident report |
| `--retest` | 5-6 | Re-verify a previous fix is still working |

---

## CRITICAL RULES

1. **NEVER deploy to production without explicit user approval.** Fix development is local; user decides when to ship.
2. **Evidence before hypotheses.** Collect logs and data BEFORE forming theories about root cause.
3. **Timestamp everything.** Every action in the timeline gets an ISO-8601 timestamp.
4. **3 attempts max per fix approach.** After 3 failures, mark as BLOCKED and escalate.
5. **Revert failed fixes immediately.** A failed fix attempt must be undone before trying another approach.
6. **Root cause uses opus.** This is the one place where deep reasoning is non-negotiable.
7. **Postmortem is not optional.** Even SEV-4 incidents get a postmortem. Document everything.
8. **Regression test is mandatory.** Stage 7 adds at least one test case. The same bug never happens twice.
9. **Report is the single source of truth.** Updated after every stage and fix attempt.
10. **Status updates every 15-30 seconds.** Incidents are time-sensitive. Keep the user informed.
11. **NEVER modify production data.** Code fixes only. Database changes need explicit approval.
12. **Sub-agents return < 500 tokens.** Full evidence writes to disk.
13. **Check git blame for the breaking change.** Most incidents are caused by recent code changes.
14. **This is a GLOBAL skill.** Works on any project, any framework, any platform.
15. **Create `.incident-reports/` directory and ensure it's gitignored.**

---

## SCORING SYSTEM

### Incident Resolution Score (Post-Fix)

| Category | Weight | What's Measured |
|----------|--------|----------------|
| Time to Triage | 15% | How quickly blast radius was assessed |
| Root Cause Accuracy | 25% | Was the actual root cause correctly identified? |
| Fix Effectiveness | 25% | Did the fix resolve the issue completely? |
| Regression Prevention | 15% | Were adequate regression tests added? |
| Documentation Quality | 10% | Is the postmortem complete and actionable? |
| Recurrence Risk | 10% | How likely is this type of incident to recur? |

### Grade Scale

| Score | Grade | Assessment |
|-------|-------|------------|
| 95-100 | A+ | Textbook incident response |
| 90-94 | A | Excellent response, minor documentation gaps |
| 85-89 | B+ | Good response, some areas for improvement |
| 80-84 | B | Adequate response, fix verified |
| 70-79 | C | Fix applied but gaps in verification or documentation |
| 60-69 | D | Fix uncertain, poor documentation, recurrence risk |
| 0-59 | F | Fix failed or incident unresolved |

---

## PRE-FLIGHT CHECKS (Stage 0 Sub-Steps)

Before incident response begins:

1. **Parse incident description** — extract key terms, affected routes/features, error types
2. **Auto-detect severity** — based on description keywords and initial signal
3. **Check for related incidents** — scan `.incident-reports/` for similar past incidents
4. **Detect project context** — framework, platform, deployment URL
5. **Check for resumable incidents** — look for in-progress incidents from last 2 hours
6. **Check cross-skill reports** — recent `/monitor`, `/qatest`, `/sec-ship` findings
7. **Create report infrastructure** — mkdir `.incident-reports/`, `evidence/`, initialize files
8. **Start timeline** — first entry: "Incident opened: [description]"

---

## STAGE 0: INITIALIZATION

### Actions

1. Parse incident description from user input
2. Auto-detect severity classification
3. Check for related/resumable incidents
4. Create report and state file infrastructure
5. Initialize timeline with first entry
6. Display incident header to user

### Report Template (Created at Stage 0)

```markdown
# Incident Report: INC-YYYYMMDD-HHMMSS

**Severity:** SEV-[1-4] ([Critical|High|Medium|Low])
**Status:** 🔴 ACTIVE
**Opened:** YYYY-MM-DD HH:MM:SS UTC
**Description:** [User's incident description]
**Resolved:** Pending

## Timeline

| Time (UTC) | Event |
|------------|-------|
| HH:MM:SS | Incident opened: [description] |

## Triage

### Blast Radius
> Pending...

### User Impact
> Pending...

## Evidence

### Runtime Logs
> Pending...

### Recent Deploys
> Pending...

### Recent Commits
> Pending...

## Root Cause Analysis
> Pending...

## Fix Attempts
> Pending...

## Fix Verification
> Pending...

## Deploy Verification
> Pending...

## Regression Tests Added
> Pending...

## Resolution Score
> Pending...

## SITREP
> Pending...
```

### State File Template

```json
{
  "skill": "incident",
  "incidentId": "INC-YYYYMMDD-HHMMSS",
  "project": "project-name",
  "description": "User's incident description",
  "severity": "SEV-2",
  "status": "active",
  "started": "ISO-8601",
  "stagesCompleted": [],
  "stagesRemaining": [1, 2, 3, 4, 5, 6, 7, 8],
  "rootCause": null,
  "fixAttempts": [],
  "timeline": [],
  "lastCheckpoint": "ISO-8601"
}
```

---

## STAGE 1: TRIAGE

**Agents:** 1-2 sonnet agents (blast radius + user impact in parallel)

### Actions

1. **Identify affected routes/endpoints:**
   - Test all routes mentioned in incident description
   - Test related routes (same feature area)
   - Map which routes return errors vs which are healthy

2. **Determine blast radius:**
   - How many routes/endpoints are affected?
   - Is the issue isolated to one feature or widespread?
   - Are there cascading failures (e.g., auth breaks everything)?

3. **Assess user impact:**
   - Check error rates in runtime logs (Vercel MCP if available)
   - Estimate number of affected users/requests
   - Determine when the issue started (first error timestamp)

4. **Identify the "since when":**
   - Check recent deployment timestamps
   - Check runtime log timeline for error onset
   - Correlate with recent git commits

5. **Update severity if needed:**
   - If triage reveals higher impact than expected, escalate
   - If triage reveals lower impact, note but don't downgrade without user approval

### Timeline Updates

```
| 14:32:15 | Triage: 3 routes affected (/api/auth, /api/auth/callback, /login) |
| 14:32:18 | Triage: Errors started at 14:22 UTC (10 minutes ago) |
| 14:32:20 | Triage: ~15 errors/minute, escalating from SEV-3 to SEV-2 |
```

---

## STAGE 2: EVIDENCE COLLECTION

**Agents:** 2 sonnet agents in parallel (logs + git history)

### Agent 1: Log Analysis

1. **Collect runtime logs:**
   - Vercel: `mcp__vercel__get_runtime_logs` with `level: ["error", "fatal"]`, last 2 hours
   - Generic: check application log files, health endpoints
   - Extract unique error messages, stack traces, request IDs

2. **Collect build logs (if recent deploy):**
   - Vercel: `mcp__vercel__get_deployment_build_logs` for latest deployment
   - Check for build warnings that might indicate issues

3. **Save full logs to evidence directory:**
   - `.incident-reports/evidence/runtime-logs.md`
   - `.incident-reports/evidence/build-logs.md`

4. **Return summary (< 500 tokens):**
   - Top 3 error messages with counts
   - Error onset timestamp
   - Error rate trend (increasing, stable, decreasing)

### Agent 2: Git & Deploy History

1. **Recent commits (last 24 hours):**
   - `git log --oneline --since="24 hours ago"`
   - Identify commits that touched files related to affected routes

2. **Recent deployments:**
   - Vercel: `mcp__vercel__list_deployments` (last 5)
   - Correlate deployment timestamps with error onset

3. **Diff analysis:**
   - `git diff` between last known good deploy and current
   - Identify changed files in affected feature areas

4. **Save to evidence directory:**
   - `.incident-reports/evidence/git-history.md`
   - `.incident-reports/evidence/deploy-history.md`

5. **Return summary (< 500 tokens):**
   - Most likely causative commit(s)
   - Deploy that corresponds to error onset
   - Files changed in affected area

---

## STAGE 3: ROOT CAUSE ANALYSIS

**Agent:** 1 opus agent (deep reasoning required)

### Input to Agent

Provide the opus agent with:
- Incident description
- Triage results (affected routes, blast radius, timeline)
- Evidence summaries from both Stage 2 agents
- Relevant source code snippets from affected files

### Analysis Process

1. **Correlate timelines:**
   - When did errors start?
   - What was deployed/committed just before?
   - Is there a clear correlation?

2. **Analyze the diff:**
   - Read the changed files in detail
   - Identify which specific change could cause the observed errors
   - Check for common patterns: missing await, undefined access, import errors, env var changes

3. **Form hypothesis with confidence level:**
   - **HIGH:** Clear correlation between a specific code change and the error
   - **MEDIUM:** Likely correlation but other factors could contribute
   - **LOW:** Multiple possible causes, more investigation needed

4. **If confidence is LOW:**
   - Request additional evidence (broader log search, more git history)
   - Consider non-code causes (infrastructure, third-party service, data issue)
   - If still low after additional analysis, trigger Human Decision

5. **Output:**
   - Root cause statement (1-2 sentences)
   - Confidence level (HIGH/MEDIUM/LOW)
   - Causative commit/change reference
   - Affected files and line numbers
   - Recommended fix approach

### Timeline Update

```
| 14:35:42 | Root cause identified (HIGH confidence): Missing await on async session validation in src/middleware.ts:42, introduced in commit abc1234 |
```

---

## STAGE 4: FIX DEVELOPMENT

**Agent:** Sequential sonnet agents (1 per fix attempt, max 3 attempts)

### Fix Loop (Mirrors /sec-ship Stage 4)

```
FOR each fix attempt (max 3):
  1. Apply fix based on root cause analysis
  2. Run build → if fails, revert and try alternative approach
  3. Run tests → if fails, revert and try alternative approach
  4. Run type-check → if fails, fix type errors
  5. If all pass → mark as FIXED, proceed to Stage 5
  6. If fails → revert, log attempt, try next approach

IF 3 attempts fail:
  → Mark as BLOCKED
  → Trigger Human Decision
  → Log all attempts with full context
```

### Fix Attempt Tracking

Each attempt is fully documented:

```markdown
#### Fix Attempt [N]/3
**Timestamp:** HH:MM:SS UTC
**Hypothesis:** [What we think will fix it]
**Files Modified:**
- `src/middleware.ts:42` — Added await to async session check

**Verification:**
- Build: ✅ | ❌
- Type-check: ✅ | ❌
- Tests: ✅ (32/32) | ❌ (30/32 — 2 failures)
- Lint: ✅ | ❌

**Result:** FIXED | REVERTED
**Duration:** Xm Xs
```

### Escalation Rules

| Attempt | What Changes |
|---------|-------------|
| Attempt 1 | Direct fix based on root cause analysis |
| Attempt 2 | Alternative approach, consider broader context |
| Attempt 3 | Simplified/conservative fix, minimize change scope |
| After 3 | BLOCKED — escalate to user with full context |

### Timeline Updates

```
| 14:36:10 | Fix attempt 1/3: Adding await to src/middleware.ts:42 |
| 14:36:45 | Fix attempt 1/3: Build passed ✅, tests passed ✅ |
| 14:36:50 | Fix marked as FIXED |
```

---

## STAGE 5: FIX VERIFICATION

**Agent:** 1 sonnet agent

### Actions

1. **Verify the fix resolves the original issue:**
   - Reproduce the original error scenario
   - Confirm the fix prevents the error
   - Test related scenarios (edge cases of the same feature)

2. **Check for regressions:**
   - Run full test suite
   - Check that unrelated features still work
   - Type-check the entire project

3. **Verify build is clean:**
   - Full production build (`npm run build` / `bun run build`)
   - Zero warnings in build output (or same warnings as before)
   - Lint passes

4. **Document verification results:**

```markdown
## Fix Verification
- **Original issue reproduced:** ✅ (confirmed 500 on /api/auth before fix)
- **Fix resolves issue:** ✅ (200 on /api/auth after fix)
- **Full test suite:** ✅ 32/32 passed
- **Build:** ✅ Clean
- **Type-check:** ✅ Zero errors
- **Lint:** ✅ Clean
```

### Timeline Update

```
| 14:38:20 | Fix verified: original issue resolved, no regressions detected |
```

---

## STAGE 6: DEPLOY VERIFICATION

**Agent:** 1 sonnet agent

### Actions

1. **Remind user to deploy the fix:**
   - Do NOT deploy automatically — the user decides when/how to deploy
   - Provide the commit/branch that contains the fix
   - Suggest: "Run `/gh-ship` to deploy the fix"

2. **After user confirms deployment:**
   - If `/monitor` skill is available, recommend running it
   - Otherwise, perform basic health checks on affected routes
   - Verify the specific error from the incident no longer occurs

3. **Check production logs (if available):**
   - Vercel: check runtime logs for error rate after deploy
   - Confirm error rate has dropped to zero or near-zero

4. **If fix doesn't work in production:**
   - Log as a new finding
   - Return to Stage 4 with production-specific context
   - Consider environment-specific differences (env vars, data, etc.)

### Timeline Update

```
| 14:45:00 | User deployed fix via /gh-ship |
| 14:47:30 | Production verification: /api/auth returning 200, error rate at 0% |
```

---

## STAGE 7: REGRESSION PREVENTION

**Agent:** 1 sonnet agent

### Actions

1. **Analyze the root cause pattern:**
   - What type of bug was this? (async error, null reference, config issue, etc.)
   - What code pattern led to this bug?
   - What test would have caught it?

2. **Write regression test(s):**
   - At minimum: one test that reproduces the exact scenario
   - Ideally: a broader test for the pattern (e.g., "all middleware functions handle async correctly")
   - Place test in the appropriate test file/directory

3. **Verify the test catches the bug:**
   - Temporarily revert the fix
   - Confirm the new test fails
   - Re-apply the fix
   - Confirm the test passes

4. **Consider additional preventive measures:**
   - Should a linting rule catch this pattern?
   - Should a pre-commit hook validate this?
   - Is there a TypeScript strict mode setting that would help?

### Timeline Update

```
| 14:50:00 | Regression test added: src/__tests__/middleware.test.ts — "should await async session validation" |
| 14:50:30 | Verified: test fails without fix, passes with fix |
```

---

## STAGE 8: POSTMORTEM

**No agent needed — orchestrator writes the postmortem from collected data.**

### Postmortem Template

Written to `.incident-reports/INC-YYYYMMDD-HHMMSS-postmortem.md`:

```markdown
# Postmortem: INC-YYYYMMDD-HHMMSS

**Date:** YYYY-MM-DD
**Severity:** SEV-[1-4]
**Duration:** [time from detection to resolution]
**Author:** Claude Code (/incident skill)

## Summary
[1-2 sentence summary of the incident]

## Impact
- **Routes affected:** [list]
- **Duration of impact:** [X minutes/hours]
- **Error count:** [N errors observed]
- **Users affected:** [estimate if available]

## Timeline

| Time (UTC) | Event |
|------------|-------|
| [Full timeline from incident report] |

## Root Cause
[Detailed explanation of what caused the incident]

### Contributing Factors
- [Factor 1: e.g., "No test coverage for async middleware behavior"]
- [Factor 2: e.g., "No pre-deploy smoke test for auth endpoints"]

## Resolution
[What was done to fix the incident]

### Fix Details
- **Commit:** [hash]
- **Files changed:** [list]
- **Change description:** [what was changed and why]

## Prevention

### Regression Tests Added
- [Test 1: description and file path]

### Action Items
- [ ] [Action 1: e.g., "Add async linting rule to eslint config"]
- [ ] [Action 2: e.g., "Add auth smoke test to CI pipeline"]
- [ ] [Action 3: e.g., "Set up /monitor as post-deploy check"]

## Lessons Learned
1. [Lesson 1]
2. [Lesson 2]

## Resolution Score: [XX]/100 ([Grade])
```

### Timeline Update

```
| 14:52:00 | Postmortem written: .incident-reports/INC-20260224-143022-postmortem.md |
| 14:52:05 | Incident resolved. Total duration: 20 minutes |
```

---

> Reference: [SITREP Standard](~/.claude/standards/SITREP_FORMAT.md) — use the unified template with domain-specific additions below.

## SITREP REQUIREMENTS

The SITREP section of the incident report must include:

### 1. Before/After Metrics Table

| Metric | During Incident | After Fix | Delta |
|--------|----------------|-----------|-------|
| Affected Routes | 3 returning 500 | 0 returning 500 | -3 errors |
| Error Rate | ~15/min | 0/min | -100% |
| Auth Success Rate | 0% | 100% | +100% |
| Build Status | N/A | ✅ Clean | N/A |
| Test Suite | 30/32 passing | 33/33 passing | +3 tests |

### 2. What Was Done

Bullet list of all actions taken, with timestamps.

### 3. Root Cause Summary

One-paragraph summary suitable for stakeholder communication.

### 4. Deferred and Why

Any items that couldn't be addressed during incident response (with conditions to revisit).

### 5. Blocked and Why

Any items that are blocked on external factors (with escalation path).

### 6. Recommendations

Prioritized list of follow-up actions to prevent recurrence.

### 7. Historical Context

- Is this a recurring incident type?
- Previous related incidents (check `.incident-reports/`)
- Trend: getting better or worse?

---

## DEFINITION OF DONE

An incident is resolved when:

- [ ] Root cause identified with HIGH or MEDIUM confidence
- [ ] Fix applied and verified locally (build + tests + type-check)
- [ ] Fix deployed to production (by user, not automatically)
- [ ] Production verified — original error no longer occurring
- [ ] At least one regression test added and passing
- [ ] Incident report complete with all sections
- [ ] Postmortem written with timeline, root cause, and action items
- [ ] State file updated with `status: "resolved"`
- [ ] SITREP written with all required sections
- [ ] Console summary displayed to user

---

## OUTPUT STRUCTURE

```
.incident-reports/
├── INC-YYYYMMDD-HHMMSS.md              # Main incident report (living document)
├── INC-YYYYMMDD-HHMMSS-postmortem.md    # Postmortem (generated at Stage 8)
├── state-YYYYMMDD-HHMMSS.json           # Machine-readable state for resume
├── evidence/                             # Collected evidence
│   ├── runtime-logs.md                   # Runtime log extracts
│   ├── build-logs.md                     # Build log extracts
│   ├── git-history.md                    # Relevant git history
│   └── deploy-history.md                 # Deployment timeline
└── history.json                          # Incident history for pattern detection
```

### History File Format

```json
{
  "incidents": [
    {
      "id": "INC-YYYYMMDD-HHMMSS",
      "date": "ISO-8601",
      "severity": "SEV-2",
      "description": "500 errors on /api/auth",
      "rootCause": "Missing await on async session check",
      "resolution": "FIXED",
      "score": 92,
      "duration": "20 minutes",
      "testsAdded": 1
    }
  ]
}
```

### Gitignore

```bash
grep -q 'incident-reports' .gitignore 2>/dev/null || echo '.incident-reports/' >> .gitignore
```

---

## CROSS-SKILL INTEGRATION

### Skills That Feed Into /incident
| Skill | What It Provides | Where to Check |
|-------|-----------------|----------------|
| `/monitor` | Health score < 60 triggers incident recommendation | `.monitor-reports/` |
| `/qatest` | Known failing tests, page health issues | `.qatest-reports/` |
| `/sec-ship` | Security vulnerabilities that may cause incidents | `.security-reports/` |

### Skills That /incident Feeds Into
| Skill | When to Recommend | Trigger |
|-------|------------------|---------|
| `/gh-ship` | Deploy the fix | After Stage 5 (fix verified) |
| `/monitor` | Verify production health post-deploy | After Stage 6 |
| `/test-ship` | Broader test coverage gaps revealed | In postmortem recommendations |
| `/sec-ship` | Security-related root cause | In postmortem recommendations |

---

## ERROR RECOVERY

### Context Reset During Active Incident
1. Read state file from `.incident-reports/state-*.json`
2. Read incident report for timeline and findings so far
3. Resume from last completed stage
4. Re-read evidence files if needed for current stage
5. Continue pipeline — do NOT re-collect evidence already gathered

### Root Cause Analysis Inconclusive
1. Try broadening the evidence search (longer time range, more files)
2. Check for non-code causes (infrastructure, data, third-party)
3. If still inconclusive, present multiple hypotheses to user with confidence levels
4. Let user choose which to pursue

### Fix Loop Exhausted (3 Attempts)
1. Mark as BLOCKED
2. Present all 3 attempts with full context to user
3. Suggest alternative approaches (rollback, feature flag, manual fix)
4. Ask user for guidance
5. Document the block in postmortem

### External Dependency Issue
1. Document that root cause is external
2. Suggest workaround if possible
3. Mark fix as DEFERRED with condition: "Blocked on [service/dependency]"
4. Complete postmortem with available information

---

## CLEANUP PROTOCOL

> Reference: [Resource Cleanup Protocol](~/.claude/standards/CLEANUP_PROTOCOL.md)

### Incident-Specific Cleanup

No resource cleanup required. All code changes are intentional fixes. Failed fix attempts are reverted immediately (Rule 5).

Cleanup actions:
1. **Gitignore enforcement:** Already handled in Stage 0
2. **Evidence files:** Keep all evidence in `.incident-reports/evidence/` — these are needed for postmortem review

---

## RELATED SKILLS

**Feeds from:**
- `/monitor` - health score below 60 or a critical route down triggers incident response; monitor is the early warning system
- `/investigate` - for deep-dive root cause analysis when initial triage reveals a complex bug

**Feeds into:**
- `/investigate` - for bugs that need deeper evidence-gathering and hypothesis testing beyond incident triage
- `/sec-ship` - if the incident turns out to be a security breach, hand off to sec-ship for the full security pipeline
- `/gh-ship` - once the fix is verified, ship it through the normal pipeline

**Pairs with:**
- `/monitor` - run monitor at the close of the incident to verify the fix actually restored production health
- `/investigate` - incident handles the urgency and postmortem; investigate handles the deep technical root cause when needed

**Auto-suggest after completion:**
When incident is resolved and postmortem is written, suggest: `/monitor` to verify production health is restored, then `/gh-ship` if code fixes need to be shipped

---

## REMEMBER

1. You are an incident responder, not just a debugger. The full lifecycle matters: triage → fix → verify → document → prevent.
2. Evidence first, hypotheses second. Never jump to fixing without understanding the problem.
3. Root cause analysis uses opus. This is the one stage where deep reasoning is worth the cost.
4. Three attempts max per fix approach. Don't spin your wheels. Escalate to the user.
5. Revert failed fixes immediately. A partial fix is worse than no fix.
6. Every incident gets a postmortem. Even SEV-4. Documentation prevents recurrence.
7. Every incident gets at least one regression test. The same bug should never happen twice.
8. The timeline is sacred. Timestamp everything. Never modify past entries.
9. Don't deploy to production. Develop the fix, verify it locally, and let the user deploy.
10. When in doubt, ask the human. A wrong fix under time pressure makes things worse.

---

<!-- Claude Code Skill by Steel Motion LLC — https://steelmotion.dev -->
<!-- Part of the Claude Code Skills Collection -->
<!-- Powered by Claude models: Haiku (fast extraction), Sonnet (balanced reasoning), Opus (deep analysis) -->
<!-- License: MIT -->
