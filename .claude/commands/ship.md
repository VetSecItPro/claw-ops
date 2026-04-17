# /ship - Full Ship Pipeline

**Test. Secure. Commit. Deploy. Verify. One command.**

This plugin chains `/test-ship`, `/sec-ship`, `/gh-ship`, and `/monitor` into a single gated pipeline. Each step must pass before the next runs. No flags, no options - it runs everything.

## INTAKE

No Socratic intake needed. If you called `/ship`, the intent is clear: get this code to production.

**One optional question (only if uncommitted changes span multiple concerns):**
> "I see changes in [areas]. Ship everything, or scope to [specific area]?"

If the user says "everything" or doesn't respond in context, ship everything.

## PIPELINE

```
STEP 1: /test-ship (write missing tests, fix failing ones)
   Gate: all tests pass, build succeeds
   Fail: fix and retry (up to 2 attempts), then stop and report
         
STEP 2: /sec-ship (full audit + fix + validate)
   Gate: no critical or high vulnerabilities remaining
   Fail: fix what's fixable, report what needs manual review, STOP
         
STEP 3: /gh-ship (commit, push, PR, CI, merge)
   Gate: PR created, CI passes, merged to main
   Fail: fix CI failures (up to 2 attempts), then stop with PR link
         
STEP 4: /monitor (verify deploy health)
   Gate: all health checks pass
   Fail: report degradation, suggest rollback if critical
```

## BEHAVIOR

- Run steps sequentially. Never skip a step.
- If Step 1 finds zero test gaps AND all tests pass, report "Tests clean" and move to Step 2 quickly.
- If Step 2 finds zero security issues, report "Security clean" and move to Step 3 quickly.
- Aggregate all reports into a single summary at the end.
- If any step fails after retries, STOP. Report what passed, what failed, and what the user needs to do.

## STATUS UPDATES

```
/ship Started
   Branch: [branch-name]
   Changes: [X files, Y insertions, Z deletions]

Step 1/4: Testing...
   [test-ship output summary]
   Gate: PASS / FAIL

Step 2/4: Security...
   [sec-ship output summary]
   Gate: PASS / FAIL

Step 3/4: Shipping...
   [gh-ship output summary]
   PR: [url]
   Gate: PASS / FAIL

Step 4/4: Verifying deploy...
   [monitor output summary]
   Gate: PASS / FAIL

/ship Complete
   Tests: X written, Y passing
   Security: clean / [N issues fixed]
   PR: [url]
   Deploy: healthy / degraded
   Duration: [X minutes]
```

## NATURAL LANGUAGE TRIGGERS

- "ship it"
- "push this to prod"
- "commit, test, and deploy"
- "full pipeline"
- "test and ship"
- "get this live"

## RELATED

- `/gh-ship` - just the git/PR/merge part (no tests, no security)
- `/test-ship` - just testing
- `/sec-ship` - just security
- `/monitor` - just health check
- `/launch-ready` - even more thorough (adds compliance, a11y, perf, QA)
