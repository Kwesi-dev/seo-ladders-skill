# /keyword-research [seed]

Keyword ideas with volume, difficulty, and DR-matched recommendations. **Seed** = `$ARGUMENTS`. Pick the mode by whether `$ARGUMENTS` is set:
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
- Pick winnable, high-intent keywords and hand them to `/write-article`, or schedule them with `/content-calendar`.
- Build topic clusters: one pillar + several supporting keywords around it.
- Pair with `/ai-visibility` prompts — write for the buyer questions where AI doesn't yet mention you.
- Pro includes 100 keyword searches/mo.
