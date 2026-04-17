# /plan - Full Planning Cycle

**Explore. Analyze. Decide. Execute. One command from idea to implementation.**

Chains `/brainstorm`, `/mdmp`, and `/subagent-dev` into a complete plan-to-execution cycle. Socratic at the start (brainstorm phase), then autonomous through planning and execution.

## INTAKE (Socratic via /brainstorm)

The `/brainstorm` skill IS the Socratic intake. It asks one question at a time, explores 2-3 approaches with tradeoffs, and only proceeds when the user approves a direction.

**The flow:**
1. User describes what they want to build
2. `/brainstorm` explores the idea with Socratic questions
3. Once direction is approved, `/mdmp` takes over with 7-lens analysis
4. User approves a COA (Course of Action) at MDMP's single pause point
5. `/subagent-dev` executes the plan with two-stage review per task

## PIPELINE

```
PHASE 1: /brainstorm (Socratic exploration)
   Purpose: clarify requirements, explore approaches, validate assumptions
   Gate: user approves a direction ("yes, do that" or similar)
   Output: approved spec document
         
PHASE 2: /mdmp (7-lens military decision-making process)
   Purpose: full analysis through SWE, PM, Security, QA, UX, DevOps, Risk lenses
   Gate: user selects a COA from the decision matrix
   Output: comprehensive plan with task list
         
PHASE 3: /subagent-dev (automated execution with review)
   Purpose: implement each task with two-stage review (spec compliance + code quality)
   Gate: all tasks complete and reviewed
   Output: working implementation
```

## BEHAVIOR

- Phase 1 is interactive (Socratic). Phases 2-3 are autonomous except for MDMP's single COA selection pause.
- If the task is clearly TACTICAL (small, obvious), suggest skipping brainstorm: "This seems straightforward. Skip brainstorm and go straight to planning?"
- If the user already has a clear spec, skip to Phase 2 directly.
- The MDMP's Risk Management lens (new) evaluates reversibility and blast radius of each COA.

## NATURAL LANGUAGE TRIGGERS

- "let's plan this"
- "plan and build"
- "design and implement"
- "I need to build X"
- "big feature"
- "new architecture"
- "let's think through this"
- "plan it out then build it"

## RELATED

- `/brainstorm` - just the exploration phase
- `/mdmp` - just the planning/analysis phase
- `/subagent-dev` - just the execution phase
