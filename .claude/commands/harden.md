# /harden - Codebase Health

**Clean dead code. Update deps. Optimize. Align design. One command.**

Chains `/cleancode`, `/deps`, `/perf`, and `/design` into a codebase health sweep. No Socratic intake - if you're hardening, you want all of it.

## PIPELINE

```
STEP 1: /cleancode (dead code, unused imports, tech debt)
   Purpose: remove cruft before optimizing
   Gate: build passes after cleanup
         
STEP 2: /deps (dependency health, CVEs, updates)
   Purpose: update vulnerable/outdated packages
   Gate: no critical CVEs, build passes after updates
         
STEP 3: /perf (performance audit + optimization)
   Purpose: bundle size, load times, API response times
   Gate: no critical regressions
         
STEP 4: /design (design system alignment)
   Purpose: consistent tokens, components, spacing
   Gate: informational (recommendations, not blockers)
   Skip: if no UI components detected
```

## BEHAVIOR

- Step 1 runs first deliberately: cleaning dead code before updating deps avoids updating packages you don't even use.
- If `/deps` finds major version upgrades that need migration work, it reports them but does NOT auto-upgrade. It only auto-upgrades patches and compatible minors.
- The `/design` step is advisory on `/harden` - it reports inconsistencies but doesn't auto-fix UI unless explicitly told to.

## NATURAL LANGUAGE TRIGGERS

- "clean up this codebase"
- "technical debt"
- "dead code"
- "update dependencies"
- "harden this"
- "codebase health check"
- "tighten things up"
- "make this cleaner"
- "maintenance pass"

## RELATED

- `/cleancode` - just dead code removal
- `/deps` - just dependency updates
- `/perf` - just performance
- `/ship` - harden + test + secure + deploy (use when you want to ship after hardening)
