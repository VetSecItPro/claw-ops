# /intel - Research and Intelligence

**Scrape. Browse. Hunt. Score. One command for any research mission.**

Chains `/scrape`, `/browse`, `/hunt`, and `/prospect` into a research pipeline. Uses Socratic intake because "research" could mean competitive intel, prospect hunting, or general web research.

## INTAKE (Socratic - 1 question)

**"What are we researching?"**
Auto-detect from context if possible. Route based on answer:

| Intent | Route |
|--------|-------|
| "competitors", "market", "what's out there" | Competitive intel: `/scrape` + `/browse` on competitor sites |
| "find customers", "prospects", "leads" | Prospect hunting: `/hunt` + `/prospect` |
| "research X topic", "what does Y say about Z" | General research: `/scrape` + `/browse` for information gathering |
| "all of it" or product/market research | Full pipeline: competitors + prospects + general research |

## PIPELINES

### Competitive Intel
```
/scrape (extract competitor sites, pricing pages, feature lists)
  -> /browse (interactive exploration of competitor products)
  -> Structured output: competitor comparison matrix
```

### Prospect Hunting
```
/hunt (scan HN, GitHub, Reddit for pain signals matching ICP)
  -> /prospect (score and rank found signals)
  -> Structured output: scored prospect list with talking points
```

### General Research
```
/scrape (extract content from specified URLs or search results)
  -> /browse (follow interesting leads interactively)
  -> Structured output: research brief with citations
```

### Full Pipeline
```
All three pipelines above, run in order.
Output: comprehensive market intelligence brief.
```

## BEHAVIOR

- Always cite sources. Every claim links to the URL it came from.
- For competitive intel: extract pricing, features, positioning, team size if available.
- For prospect hunting: requires `icp.json` to exist. If it doesn't, run `/prospect` first to define the ICP.
- For general research: respect rate limits, don't hammer sites, use SearXNG for search.
- Output goes to `.intel-reports/` directory (gitignored).

## NATURAL LANGUAGE TRIGGERS

- "research this"
- "competitive analysis"
- "what are competitors doing?"
- "find me prospects"
- "market research"
- "look into X"
- "intel on X"
- "what's the landscape?"
- "who else does this?"
- "dig into this"

## RELATED

- `/scrape` - just web extraction
- `/browse` - just interactive browsing
- `/hunt` - just pain-signal discovery
- `/prospect` - just ICP/lead scoring
- `/gtm` - intel as part of a full go-to-market campaign
