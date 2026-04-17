# /content - End-to-End Content Pipeline

**Write. Edit. Cut. Distribute. One command for any content piece.**

Chains `/blog`, `/newsletter`, `/copy`, `/narrative`, `/critique-adversarial`, and `/social` based on what you need. This is the one plugin that uses Socratic intake because "content" could mean anything.

## INTAKE (Socratic - 2 questions max)

**Question 1: "What are we creating?"**
Auto-detect from context if possible. If ambiguous, ask:
> "What kind of content? (article/newsletter/social posts/landing page copy/email sequence)"

Route based on answer:
- Article/blog post -> `/blog` pipeline
- Newsletter -> `/newsletter` pipeline
- Social posts -> `/social` pipeline
- Landing page/email/ad -> `/copy` pipeline
- "All of it" -> `/blog` first, then repurpose to all channels

**Question 2 (only if creating from scratch): "What's the topic or angle?"**
> "Give me the topic, angle, or just paste the rough idea."

If the user already provided context (e.g., "write a piece about X"), skip this question entirely.

## PIPELINES (by content type)

### Article Pipeline
```
/blog (research, outline, draft, voice pass, anti-slop scrub)
  -> /critique-adversarial (hostile edit - cut what doesn't serve the argument)
  -> /social (repurpose to LinkedIn, X, etc.)
```

### Newsletter Pipeline
```
/newsletter (structure issue, develop voice, build sections)
  -> /critique-adversarial (tighten prose)
  -> /social (teaser posts for distribution)
```

### Social Pipeline
```
/social (platform-specific posts, content calendar)
  -> /narrative (add storytelling where flat)
```

### Copy Pipeline
```
/copy (conversion-optimized output for the asset type)
  -> /narrative (inject story structure where appropriate)
```

### Full Pipeline ("all of it")
```
/blog (the anchor piece)
  -> /critique-adversarial (edit pass)
  -> /newsletter (adapt for newsletter format)
  -> /social (repurpose to all platforms)
  -> /copy (extract CTAs, email snippets, ad hooks)
```

## BEHAVIOR

- The `/critique-adversarial` step is non-optional for articles and newsletters. Every long-form piece gets the hostile editor.
- `/narrative` runs as a sub-skill inside `/copy` and `/social` when storytelling is the mechanism, not as a standalone step.
- Voice consistency: all outputs anchor to `voice-dna.md` if it exists in the workspace.
- No AI slop. The `/blog` pipeline includes an anti-slop scrub. If slop survives, `/critique-adversarial` catches it.

## NATURAL LANGUAGE TRIGGERS

- "write a piece about X"
- "create content about X"
- "write and publish"
- "blog post about X"
- "newsletter issue"
- "social posts for X"
- "landing page copy"
- "email sequence"
- "content calendar"
- "write this up"

## RELATED

- `/blog` - just the article pipeline
- `/social` - just social posts
- `/copy` - just conversion copy
- `/newsletter` - just newsletter
- `/gtm` - content as part of a full go-to-market campaign
