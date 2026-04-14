# Skill Chains - Ecosystem Map

Visual reference of how all skills connect. Use this to chain skills deliberately rather than running them in isolation.

---

## Chain 1: Planning and Execution

The primary build chain from idea to shipped code.

```
/brainstorm
    |
    v
/mdmp  (structure the plan, get COA approval)
    |
    v
/subagent-dev  (parallel task execution with review)
    |
    v
/smoketest  (quick sanity check)
    |
    v
/test-ship  (full test audit)
    |
    v
/sec-ship  (security audit)
    |
    v
/gh-ship  (commit, PR, merge, deploy)
    |
    v
/monitor  (post-deploy health check)
```

---

## Chain 2: Design and Quality

For UI work - design through accessibility and performance.

```
/design
    |
    +----> /a11y   (accessibility audit)
    |
    +----> /perf   (performance audit)
    |
    +----> /browse (visual verification)
    |
    v
/gh-ship
```

---

## Chain 3: Marketing Suite

Full content pipeline from strategy to distribution.

```
/scrape  (brand/competitor research - optional entry point)
    |
    v
/marketing  (strategy, ICP, messaging, channel plan, brand guide via Module F)
    |
    +----> /copy   (landing page, email, ad copy)
    |
    +----> /blog   (long-form content)
    |
    +----> /social (social posts, calendar)
```

---

## Chain 4: Build and Ship Pipeline

Pre-ship quality gates - run in parallel or sequence.

```
/test-ship  (unit, integration, E2E tests)
    |
/sec-ship   (static security audit)
    |
/deps       (dependency health)
    |
/cleancode  (dead code, tech debt)
    |
    v
/gh-ship
    |
    v
/monitor
```

---

## Chain 5: Incident Response

From alert to root cause to fix.

```
/monitor  (detects anomaly or alert)
    |
    v
/incident  (structured triage and response)
    |
    v
/investigate  (root-cause analysis)
    |
    v
/sec-ship  (if security issue)
    |
    v
/gh-ship  (fix deployed)
    |
    v
/monitor  (verify fix in production)
```

---

## Chain 6: Launch Readiness

/launch orchestrates all gates before a product ships.

```
/sec-ship        (security)
/deps            (supply chain)
/compliance      (privacy/data)
/perf            (performance)
/a11y            (accessibility)
/cleancode       (code quality)
/test-ship       (test coverage)
/docs            (documentation)
        \
         \----> /launch  (scores all 8 gates, GO/NO-GO verdict)
                    |
                    v
                /gh-ship
                    |
                    v
                /monitor
```

---

## Chain 7: Security Deep-Dive

Thorough security pass combining static analysis and active exploitation.

```
/deps       (vulnerable packages)
    |
/sec-ship   (static code analysis)
    |
/redteam    (active exploitation testing)
    |
    v
findings consolidated into /sec-ship report
    |
    v
/compliance (re-evaluate posture if needed)
    |
    v
/gh-ship
```

---

## Chain 8: Skill Development

Create a new skill using TDD methodology.

```
/brainstorm  (define what the skill needs to do)
    |
    v
/write-skill  (RED-GREEN-REFACTOR cycle)
    |
    v
/smoketest   (verify project still healthy)
    |
    v
/gh-ship     (commit and register new skill)
```

---

## Full Ecosystem Table

| Skill | Feeds From | Feeds Into | Natural Pairs |
|---|---|---|---|
| /a11y | /design, /browse | /gh-ship, /launch | /design, /qatest |
| /blog | /marketing, /copy | /social | /gh-ship, /docs |
| /brainstorm | - | /subagent-dev, /mdmp, /write-skill | /design, /docs |
| /browse | /dev, /design | /a11y, /perf, /qatest, /investigate | /design, /qatest |
| /cleancode | - | /gh-ship, /perf | /test-ship, /docs, /launch |
| /compliance | - | /compliance-docs, /sec-ship, /gh-ship | /compliance-docs, /launch |
| /compliance-docs | /compliance, /sec-ship | - | /compliance, /sec-ship |
| /copy | /marketing | /blog, /social | /marketing, /design |
| /db | /brainstorm, /mdmp, /migrate | /gh-ship, /compliance, /sec-ship | /migrate, /test-ship |
| /deps | - | /gh-ship, /test-ship, /sec-ship | /sec-ship, /migrate, /launch |
| /design | /brainstorm, /browse | /a11y, /perf, /browse, /copy | /a11y, /copy |
| /dev | - | /browse, /qatest, /redteam | /smoketest, /browse |
| /docs | /brainstorm, /subagent-dev, /migrate | /gh-ship, /launch | /cleancode, /copy |
| /gh-ship | /test-ship, /sec-ship, /smoketest, /subagent-dev | /monitor | /qatest, /monitor |
| /incident | /monitor, /investigate | /investigate, /sec-ship, /gh-ship | /monitor, /investigate |
| /investigate | /incident, /monitor, /qatest | /gh-ship, /incident, /sec-ship, /mdmp | /browse, /smoketest |
| /launch | /sec-ship, /deps, /compliance, /perf, /a11y, /cleancode, /test-ship, /docs | /gh-ship | /monitor, /qatest |
| /marketing | /scrape | /copy, /blog, /social | /copy, /design, /scrape |
| /scrape | - | /marketing, /prospect, /design, /blog, /compliance-docs | /browse, /marketing |
| /mdmp | /brainstorm, /investigate | /subagent-dev, /gh-ship, /docs | /brainstorm, /subagent-dev |
| /migrate | /deps, /brainstorm | /test-ship, /docs, /db | /deps, /test-ship |
| /monitor | /gh-ship, /qatest, /sec-ship | /incident, /perf, /sec-ship, /qatest | /gh-ship, /incident |
| /perf | /design, /monitor, /launch | /gh-ship, /launch | /a11y, /cleancode |
| /qatest | /subagent-dev, /test-ship, /browse | /gh-ship, /investigate, /launch | /a11y, /smoketest |
| /redteam | /dev, /sec-ship, /subagent-dev | /sec-ship, /investigate, /compliance | /sec-ship, /qatest |
| /sec-ship | /subagent-dev, /redteam, /deps, /incident | /gh-ship, /compliance, /compliance-docs, /launch | /redteam, /deps |
| /sec-weekly-scan | /sec-ship, /monitor | /sec-ship, /gh-ship | /deps, /compliance |
| /smoketest | /dev, /subagent-dev | /test-ship, /gh-ship, /investigate | /dev, /qatest |
| /social | /marketing, /blog | - | /copy, /blog |
| /subagent-dev | /brainstorm, /mdmp | /test-ship, /sec-ship, /gh-ship | /smoketest, /qatest |
| /test-ship | /subagent-dev, /smoketest, /migrate | /gh-ship, /sec-ship, /launch | /sec-ship, /qatest |
| /write-skill | /brainstorm, /mdmp | /gh-ship | /brainstorm, /smoketest |

---

## Quick Reference: "What do I run next?"

| Just finished... | Consider running... |
|---|---|
| /brainstorm | /mdmp (structure it) or /subagent-dev (execute it) |
| /subagent-dev | /smoketest (quick check) or /test-ship (thorough) |
| /design | /a11y + /browse (quality gates) |
| /test-ship | /sec-ship (security gate) |
| /sec-ship | /gh-ship (ship it) |
| /gh-ship | /monitor (verify deploy) |
| /monitor | /incident (if alerts) or back to /brainstorm |
| /marketing | /copy + /blog + /social (content suite) |
| /scrape | /marketing Module F (brand guide) or /prospect (competitor research) |
| /blog | /social (distribution) |
| /migrate | /test-ship (verify no regressions) |
| /incident | /investigate (root cause) |
| /investigate | /sec-ship or /gh-ship (fix and ship) |
| /launch | /gh-ship (final ship) |
