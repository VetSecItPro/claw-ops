# /ops - Operations: Debug, Respond, Monitor

**Something's wrong? This figures out what and runs the right skill.**

Auto-routes between `/investigate`, `/incident`, `/monitor`, and `/dev` based on severity. No Socratic intake - it reads the situation and acts.

## AUTO-ROUTING

```
Is production down or users affected?
  YES -> /incident (triage, fix, verify, postmortem)
  NO  -> Is something broken locally?
           YES -> /investigate (evidence-first root cause debugging)
           NO  -> Is a service unhealthy or degraded?
                    YES -> /monitor (health check, identify degradation)
                    NO  -> /dev (start/restart dev server)
```

## DETECTION SIGNALS

| Signal | Routes To | Why |
|--------|-----------|-----|
| "production is down", "users are reporting", "urgent" | `/incident` | Production impact requires incident protocol |
| "not working", "broken", "can't figure out", "bug" | `/investigate` | Local bug needs evidence-first debugging |
| "is it healthy?", "check the deploy", "anything wrong?" | `/monitor` | Health verification |
| "start it up", "run the app", "dev server" | `/dev` | Development server management |
| "something's off but I'm not sure what" | `/monitor` first, then `/investigate` if issues found | Triage before deep dive |

## BEHAVIOR

- Always start with a 10-second situation assessment: git status, running processes, recent logs
- Route to the right sub-skill without asking
- If the initial routing was wrong (e.g., started `/investigate` but it's actually a production issue), escalate automatically to `/incident`
- After resolution, suggest `/monitor` to verify the fix in production

## NATURAL LANGUAGE TRIGGERS

- "something's broken"
- "it's not working"
- "production is down"
- "can you check on things?"
- "what's the status?"
- "is everything healthy?"
- "start the server"
- "fix this"
- "why is this happening?"
- "debug this"

## RELATED

- `/investigate` - just root-cause debugging
- `/incident` - just production incident response
- `/monitor` - just health check
- `/dev` - just dev server
