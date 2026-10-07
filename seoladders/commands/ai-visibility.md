# /ai-visibility

Your visibility across AI engines — whether all six tracked engines recommend you (ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode). This is the differentiator. Lead with it.

The product has two AI-visibility features, and the dashboard names them separately: **Prompt Tracking** (everything below except the last section: answers to the prompts the site tracks) and **AI Mentions** (`/ai-visibility/mentions`: where AI already mentions or cites the site across all monitored answers). Their numbers differ on purpose. When you report them, say which one each number comes from.

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

- **`hasData == false`** → the monitor is created for you during onboarding and runs on a schedule (every few days), so this means the first run has not landed yet — not that the user must do something. **Pro has no manual runs**, so do not send them to a Run button: it will refuse them.
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

## AI Mentions: beyond the prompts you track (`view: mentions`, its own page in the dashboard)

The views above all come from the prompts you track. **AI Mentions** answers a different question: across the AI answers we monitor, where do Google AI and ChatGPT **already** mention or cite your site? It needs no setup and updates on its own. Don't tell the user how often it updates.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/mentions | jq '{state, totals, changeSinceLast, pages: .pages[:5], competitors}'
```

- **`state`**: `ready`, `building` (the first check has just started; call again in a minute or two), `failed` (retried within a day; say so rather than calling it broken), or `unavailable` (no active subscription).
- **`totals`**: `{mentions, aiSearchVolume, byEngine}`. Coverage is **Google AI Overviews, plus ChatGPT for US English sites only**. Do not describe this as all six engines.
- **`queries`**: every question you appear in. **`cited: true`** means one of your pages is a source of the answer; **`cited: false`** means AI *names* you but links elsewhere. Those named-only questions are the best targets to turn into citations.
- **`pages`**: your pages AI uses as a source, with how many questions each answers. `writtenBySeoladders: true` means it is an article SEOLadders published.
- **`competitors`**: you and your tracked competitors, by brand-name mentions. `isYou: true` is you.
- **`nicheTopDomains`**: filled only when you have no mentions yet. The sites AI cites for your topics.
- **Trials** get one snapshot; it updates once they subscribe. Reading it is not metered.
