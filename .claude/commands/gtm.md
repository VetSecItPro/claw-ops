# /gtm - Full Go-to-Market

**ICP. Prospect. Campaign. Copy. Distribution. One command to launch.**

Chains `/icp-from-repo`, `/prospect`, `/campaign`, `/marketing`, `/copy`, `/social`, `/hunt`, and `/outreach` into a full go-to-market machine. Uses Socratic intake because GTM needs to know what you're selling and to whom.

## INTAKE (Socratic - 3 questions max)

**Question 1: "What product or feature are we taking to market?"**
> Auto-detect from repo if possible (reads package.json, README, landing page). If unclear, ask.

**Question 2: "Who's the buyer?"**
> If `icp.json` exists, use it. If not: "Describe your ideal customer in one sentence."

**Question 3: "What stage are we at?"**
> - **Pre-launch** (no customers yet) -> full pipeline from ICP through campaign
> - **Soft launch** (some beta users) -> focus on outreach + content
> - **Growth** (product-market fit) -> focus on scaling channels

## PIPELINE

```
STAGE 1: Intelligence (who are we selling to?)
   /icp-from-repo (extract product context)
   /prospect (define ICP, score leads)
   /hunt (find pain signals in the wild)
         
STAGE 2: Strategy (how do we reach them?)
   /marketing (strategy, messaging framework, channel plan)
   /campaign (90-day timeline, KPIs, week-by-week plan)
         
STAGE 3: Assets (what do we put in front of them?)
   /copy (landing pages, emails, ad copy)
   /social (platform-specific posts, content calendar)
   /outreach (personalized DMs/emails for top prospects)
```

## BEHAVIOR

- Stage 1 feeds Stage 2: ICP and prospect data inform the marketing strategy.
- Stage 2 feeds Stage 3: the campaign brief tells copy/social what to produce.
- Each stage produces artifacts that persist (icp.json, scoring.json, campaign brief).
- If `/outreach` is still in DRAFT SPEC status, skip it and note "outreach drafts available when /outreach ships."
- For "pre-launch" stage, run all three stages in order.
- For "soft launch", skip Stage 1 if ICP exists, focus on Stages 2-3.
- For "growth", skip strategy (assume it exists), focus on asset production.

## NATURAL LANGUAGE TRIGGERS

- "go to market"
- "GTM strategy"
- "launch marketing"
- "how do we sell this?"
- "find customers"
- "marketing campaign"
- "who should we target?"
- "build the funnel"
- "sales pipeline"
- "launch strategy"

## RELATED

- `/marketing` - just strategy/messaging (no ICP research or asset production)
- `/campaign` - just the 90-day plan
- `/content` - just content creation (no ICP/prospect/strategy)
- `/prospect` - just ICP/lead scoring
