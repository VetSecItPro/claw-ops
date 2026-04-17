# /quality - Full Quality Sweep

**Tests. QA. Smoke. Performance. Accessibility. Everything quality, one command.**

Chains `/test-ship`, `/qatest`, `/smoketest`, `/perf`, and `/a11y` into a comprehensive quality gate.

## INTAKE

No Socratic intake. Quality means everything.

**Smart fast-path:** If the codebase has no UI (pure API/CLI), skip `/a11y` and UI-specific `/qatest` steps automatically.

## PIPELINE

```
STEP 1: /smoketest (quick sanity - 2-3 min)
   Purpose: catch obvious breakage before investing in deep analysis
   Gate: build passes, no runtime crashes
   Fail: fix immediately, then continue
         
STEP 2: /test-ship (comprehensive test audit)
   Purpose: find gaps, write missing tests, fix failing ones
   Gate: all tests pass, coverage acceptable
   Fail: fix and retry (up to 2 attempts)
         
STEP 3: /perf (performance audit)
   Purpose: Lighthouse, bundle size, Core Web Vitals, API response times
   Gate: no critical performance regressions
   Fail: report with specific optimization recommendations
         
STEP 4: /a11y (accessibility audit)
   Purpose: WCAG compliance, keyboard nav, screen readers, contrast
   Gate: no critical a11y violations
   Fail: fix what's auto-fixable, report manual fixes needed
   Skip: if no UI components detected
         
STEP 5: /qatest (full autonomous QA)
   Purpose: crawl all pages, test all interactions, validate all APIs
   Gate: no critical failures
   Fail: auto-fix what's possible, report remaining issues
```

## QUALITY REPORT

```
QUALITY REPORT
Date: [date]
Duration: [X minutes]
Overall: PASS / CONDITIONAL PASS / FAIL

Smoke Test:     PASS/FAIL
Test Coverage:  [X]% ([N] tests, [N] new)
Performance:    [Lighthouse score] / [bundle size] / [LCP]
Accessibility:  [N] violations ([N] critical, [N] fixed)
QA:             [N] pages tested, [N] interactions, [N] failures

Action Items:
   [numbered list of remaining issues by severity]
```

## NATURAL LANGUAGE TRIGGERS

- "full QA"
- "test everything"
- "is this ready?"
- "quality check"
- "run all tests"
- "check everything before shipping"
- "QA this"
- "how's the quality?"

## RELATED

- `/ship` - quality + security + deploy (the full pipeline)
- `/smoketest` - just the quick sanity check
- `/launch-ready` - quality + security + compliance (pre-launch)
