# /ai-visibility

Your visibility across AI engines — whether ChatGPT, Perplexity, Gemini, Claude, and Google AI recommend you. This is the differentiator. Lead with it.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq .
```

## How to read it

- **`hasData`** — `false` means no monitor has run yet (see below).
- **`visibilityScore`** — 0–100, how often AI engines mention you across the prompts you track. Headline number.
- **`perEngine`** — `[{engine, pct, mentioned, total}]` — your mention rate per engine. Find the weakest engine.
- **`shareOfVoice`** — `[{name, isBrand, domain, mentions, pct}]` — you vs. competitors in AI answers. `isBrand:true` is you. If a rival has more `pct`, that's the gap to close.
- **`sentiment`** — how positively AI describes you when it does mention you.
- **`topCitations`** — `[{domain, count, owned, pct}]` — the sources AI cites when answering these prompts. `owned:false` = a source you should earn a mention/link on. `owned:true` = your own pages already being cited.
- **`lastRunAt`** — freshness of the data.

## What to do with the result

- **`hasData == false`** → tell the user to **create an AI monitor and run it on the dashboard** (Dashboard → AI Visibility). No API call writes monitors.
- Report `visibilityScore`, the weakest engine in `perEngine`, and the top competitor in `shareOfVoice`.
- Use `topCitations` where `owned:false` as outreach targets — get cited on the sources AI already trusts.
- Next steps: `/prompts` (track the right buyer questions), `/actions` (act on the gaps). See `references/ai-visibility-playbook.md`.

## Drill into the detail (sub-views)

The overview is a summary — pull the full data with these:

```bash
# Citation sources AI pulls from (earn links where owned:false)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/citations | jq '.citations[]'

# Content gaps — buyer questions where you're absent (write these next)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq '.gaps[]'

# Sentiment — how positively AI describes you (current, trend, by engine/type)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/sentiment | jq '{current, distribution, byEngine}'

# Sources grouped: socials (Reddit/YouTube/G2…), offsite third parties, your own pages
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/sources | jq '{socials, offsite, pages}'
```
