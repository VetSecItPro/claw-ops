# /fortress - Complete Security Posture

**Audit. Fix. Pen-test. Comply. Document. Everything security, one command.**

This plugin chains `/sec-ship`, `/compliance`, `/redteam`, `/compliance-docs`, and `/sec-weekly-scan` into a full security posture assessment. No flags - it runs the works.

## INTAKE

No Socratic intake. If you called `/fortress`, you want the full treatment.

**Context-aware scoping:**
- If there are uncommitted changes on the branch: focus the audit on those changes first, then expand to full codebase
- If no uncommitted changes: full codebase audit from the start

## PIPELINE

```
STEP 1: /sec-ship (audit + fix + validate)
   Scope: full codebase - OWASP Top 10, auth, data exposure, input validation
   Output: vulnerabilities found, fixed, remaining
   Gate: no critical vulnerabilities remaining
         
STEP 2: /compliance (privacy + data compliance)
   Scope: GDPR, CCPA, data handling, consent, retention
   Output: compliance gaps, recommendations
   Gate: informational (does not block)
         
STEP 3: /redteam (active exploitation testing)
   Scope: localhost only - injection, auth bypass, privilege escalation
   Output: exploits found, proof of concept
   Gate: no exploitable critical vulnerabilities
   NOTE: only runs if user confirms ("This runs active attacks against localhost. Proceed?")
         
STEP 4: /compliance-docs (generate documentation)
   Scope: security policy, incident response plan, data handling docs
   Output: generated compliance documents
   Gate: informational (does not block)
         
STEP 5: /sec-weekly-scan (dependency + supply chain)
   Scope: all dependencies, known CVEs, license issues
   Output: vulnerable packages, upgrade paths
   Gate: no critical CVEs in production dependencies
```

## BEHAVIOR

- Steps 1-2 always run. Step 3 requires explicit confirmation (active exploitation). Steps 4-5 always run.
- If Step 1 finds and fixes vulnerabilities, re-run the validation to confirm fixes hold.
- Aggregate all findings into a single Fortress Report with severity ratings.
- If the codebase is clean across all steps, say so clearly: "Fortress audit complete. No critical findings."

## FORTRESS REPORT

```
FORTRESS SECURITY REPORT
Date: [date]
Codebase: [repo]
Duration: [X minutes]

OVERALL POSTURE: STRONG / ADEQUATE / NEEDS WORK / CRITICAL

Vulnerabilities:
   Critical: [N] found, [N] fixed, [N] remaining
   High:     [N] found, [N] fixed, [N] remaining
   Medium:   [N] found, [N] fixed, [N] remaining
   Low:      [N] found

Compliance:
   GDPR: [status]
   CCPA: [status]
   Data handling: [status]

Red Team:
   Exploits attempted: [N]
   Successful: [N]
   [details of any successful exploits]

Dependencies:
   CVEs found: [N]
   Upgradeable: [N]
   Action required: [details]

Documents Generated:
   [list of compliance docs created]
```

## NATURAL LANGUAGE TRIGGERS

- "full security review"
- "security audit everything"
- "lock this down"
- "is this secure?"
- "OWASP check"
- "pen test this"
- "security posture"
- "vulnerability scan"
- "how secure are we?"

## RELATED

- `/sec-ship` - just the audit/fix cycle (fastest)
- `/redteam` - just active exploitation
- `/compliance` - just privacy/data compliance
- `/compliance-docs` - just documentation generation
