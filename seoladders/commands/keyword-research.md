# /keyword-research [seed]

Keyword ideas with volume, difficulty, and DR-matched recommendations. **Seed** = `$ARGUMENTS`.

## First: show what the agent already found (free)

The agent researches keywords on its own after onboarding and keeps them in topic clusters, with articles scheduled from them. Before spending a search, read those:

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/keywords/found | jq '{summary, topics: [.topics[] | {name, keywords: .keywords[:5]}], dashboard}'
```

(MCP: `my-keywords`.) Lead with it: "Your agent already picked N keywords across M topics, and K are scheduled this month", then link `dashboard.topics`. Run a search below only if the user wants something those clusters don't cover, or names a seed that isn't in them.

## Searching

Pick the mode by whether `$ARGUMENTS` is set:
- `$ARGUMENTS` **present** → Mode 1 (manual search on that seed).
- `$ARGUMENTS` **empty** → Mode 2 ("find keywords for me", auto from the site).

## Mode 1 — Manual search (seed provided)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"seed":"$ARGUMENTS","longTail":false,"filterByDR":true}' \
  https://www.seoladders.com/api/v1/keywords/search | jq '{count, domainRank, recommendedKdCap, keywords: .keywords}'
```

- Body: `{ seed: string (required), longTail?: boolean, filterByDR?: boolean }`.
- Returns `{ keywords, count, domainRank, recommendedKdCap, isLocalBusiness, usage }`.
- Set `filterByDR: true` to only get keywords your domain rating can realistically win.

## Mode 2 — Find keywords for me (auto, no seed)

Use when the user says "find keywords for me" / doesn't have a seed. Auto-discovers rankable keywords from the site's own context (topics, GSC, competitors) — no body required.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/keywords/discover | jq '{seed, count, keywords: .keywords}'
```

- Returns `{ seed, keywords, count, usage }` — `seed` is the topic we inferred for the site.

## How to read it

- Each keyword carries **volume** (monthly searches), **difficulty** (KD), and a **DR match** — whether your domain rating can realistically win it.
- `recommendedKdCap` (manual mode) is the difficulty ceiling we recommend for your domain — prefer keywords under it. High volume + KD above your DR is a slow climb.

## What to do with the result

- Ask the user which mode they want if it's unclear: "give me a keyword, or want me to find some for you?"
- Pick winnable, high-intent keywords and schedule them with `/content-calendar`; the agent writes them.
- Build topic clusters: one pillar + several supporting keywords around it.
- Pair with `/ai-visibility` prompts — write for the buyer questions where AI doesn't yet mention you.
- **Metered:** each search uses one unit of the monthly keyword research allowance, shared with the dashboard, `/rankings`, `/keyword-lookup` and `/serp-check`. A `429` means it is used up for this billing cycle: read its `instructions_for_agent` to the user and don't retry.
