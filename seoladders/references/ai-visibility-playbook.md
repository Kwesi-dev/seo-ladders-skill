# AI Visibility Playbook (GEO / AEO)

This is the differentiator. Most SEO skills stop at writing articles. This one measures and grows whether **AI engines recommend you** — ChatGPT, Perplexity, Gemini, Claude, Google AI — then closes the gap. Run this loop continuously.

## Why it matters

Buyers now ask an AI engine before they ask Google. If the AI's answer names a competitor and not you, you lost the deal before the click. AI visibility is the new top of funnel. SEO still feeds it (AI cites the pages that rank), but you have to measure the answer itself.

## The loop

### 1. Pick the right prompts (GSC-derived first)

The prompts you track are the buyer questions you want AI to answer with your name in it. The best source is your **real Google Search Console queries** — questions people already reach you with.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/prompts/explorer?source=gsc" | jq '.suggestions[]'
```

- Fall back to `source=keywords` or `source=paa` if GSC isn't connected (`needsConnect:true`).
- Add high-intent, bottom-of-funnel questions ("best X for Y", "X alternatives", "is X worth it") — that's where being named decides the sale.
- Respect the cap (Pro = 30). Swap low-value prompts when full.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompts":["best ai seo tool","seoladders alternatives"]}' \
  https://www.seoladders.com/api/v1/prompts | jq '{added, skipped, remaining}'
```

### 2. Read your visibility

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq .
```

- **`visibilityScore`** — the headline. Track it over time; it should climb as you act.
- **`perEngine`** — find your weakest engine (`pct`, `mentioned/total`). Some engines lean on different sources — a weak engine usually means you're missing the sources *that* engine cites.
- **`shareOfVoice`** — you (`isBrand:true`) vs competitors. If a rival's `pct` beats yours, study where they're cited and earn the same.
- **`sentiment`** — being mentioned negatively is its own problem; fix the narrative on the sources driving it.
- **`topCitations`** — the sources AI trusts for these prompts. `owned:false` = an outreach target. `owned:true` = your pages already winning — make more like them.

> `hasData:false` → create a monitor and run it on the dashboard first. No API endpoint writes monitors.

### 3. Earn citations from the sources AI already trusts

`topCitations` is your outreach map. AI cites a small set of trusted domains per topic. Get mentioned/linked on those — listicles, comparison posts, review sites, Reddit/forums, docs — and your mention rate rises across every engine that leans on them.

- Sort `topCitations` by `pct`, filter `owned:false`, attack the top few.
- `/actions` turns this into concrete `outreach` and `reddit` tasks.

### 4. Write content for the gaps

For prompts where you're absent and no owned page is cited, the fix is content: write the page AI *should* cite.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"best ai seo tools"}' \
  https://www.seoladders.com/api/v1/articles | jq .
```

- Answer the prompt directly and early (AI extracts answers, not fluff).
- Use clear comparisons, structured FAQs, and schema — extractable formats get cited.
- Build clusters around each high-value prompt.

### 5. Act on recommendations

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/actions?status=suggested" | jq '.actions[] | {priority, type, title}'
```

- `outreach` / `reddit` → earn citations (step 3).
- `content` → write the gap (step 4).
- `setup` → finish a connection.

## Cadence

1. Connect GSC → derive prompts → add the high-intent ones (respect cap).
2. Run the monitor (dashboard) → read `/ai-visibility`.
3. Each cycle: earn citations on `owned:false` sources, write gap content, work `/actions` by priority.
4. Re-check `visibilityScore` and `shareOfVoice` — confirm the line is going up, then repeat.
