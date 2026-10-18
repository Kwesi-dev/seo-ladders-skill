# /ai-visibility

Your visibility across AI engines — whether all six tracked engines recommend you (ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode). This is the differentiator. Lead with it.

The product has two AI-visibility features, and the dashboard names them separately: **Prompt Tracking** (everything below except the last section: answers to the prompts the site tracks) and **AI Mentions** (`/ai-visibility/mentions`: where AI already mentions or cites the site across all monitored answers). Their numbers differ on purpose. When you report them, say which one each number comes from.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq .
```

## How to read it

- **`hasData`** — `false` means no monitor has run yet (see below).
- **`visibilityScore`** — the AI Visibility Score, 0–100, the same number the dashboard shows. Headline number. It is blended from three signals so one answer changing does not swing it: the tracked prompts over the last two weeks (named counts, linked to the site counts more), AI Mentions, and visits from AI assistants when Google Analytics is connected.
- **`visibilityParts`** — `{prompts, mentions, traffic}`, each 0–100, or `null` when that signal has no data yet (`traffic: null` = Google Analytics not connected). Use it to say *why* the score moved.
- **`namedRate`** — the latest scan alone: % of answers to prompts that don't name the brand that named it. It swings from scan to scan; report `visibilityScore`, not this.
- **`perEngine`** — `[{engine, pct, mentioned, total}]` — your mention rate per engine, over the last two weeks, on prompts that don't name the brand. Find the weakest engine.
- **`buyingJourney`** — `{stages: [{stage, label, citations, named, answers, status, missingKeyPage, topPages}]}` — which of the site's pages AI links to while buyers learn (`learn`), compare (`compare`) and check price and fit (`decide`). `status: "no_page"` with `missingKeyPage` (e.g. `"pricing page"`) means the site has no such page for AI to cite: that is work for the owner, since the agent cannot add pages.
- **`shareOfVoice`** — `[{name, isBrand, domain, mentions, pct}]` — you vs. competitors in AI answers. `isBrand:true` is you. If a rival has more `pct`, that's the gap to close.
- **`sentiment`** — how positively AI describes you when it does mention you.
- **`topCitations`** — `[{domain, count, owned, pct}]` — the sources AI cites when answering these prompts. `owned:false` = a source you should earn a mention/link on. `owned:true` = your own pages already being cited.
- **`lastRunAt`** — freshness of the data.

## What to do with the result

- **`hasData == false`** → the monitor is created for you during onboarding and reruns on a schedule throughout the week (a trial gets one scan), so this means the first run has not landed yet — not that the user must do something. **Pro has no manual runs**, so do not send them to a Run button: it will refuse them.
- Report `visibilityScore` (with the part that moved it, from `visibilityParts`), the weakest engine in `perEngine`, and the top competitor in `shareOfVoice`.
- If a `buyingJourney` stage is `no_page`, tell the user which page is missing. If a stage has `citations` but `named: 0`, AI uses their pages as a source while recommending someone else: say so.
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
  https://www.seoladders.com/api/v1/ai-visibility/sentiment | jq '{current, distribution, byEngine, accuracy}'
# `accuracy.claims[]`: what AI states about you (price, integrations...) checked
# against your knowledge base, with `verdict` and, for wrong ones, `likelySources`:
# your pages and articles that still say the wrong thing. Fix the source, or
# correct the knowledge base with /knowledge. Applying an article fix is done in
# the dashboard.

# Sources grouped: socials (Reddit/YouTube/G2…), offsite third parties, your own pages
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/sources | jq '{socials, offsite, pages}'
```

## What each engine actually answered (`prompt-answers`)

The evidence behind the score: for each tracked prompt, what every engine said in the latest scan.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/ai-visibility/prompt-answers?limit=10" \
  | jq '{summary, prompts: [.prompts[] | {text, mentionedIn, answers: [.answers[] | {engineLabel, mentioned, position, competitorsMentioned, excerpt}]}]}'
```

(MCP: `prompt-answers`; `promptId` or `engine` to narrow.) Each answer has whether the brand is named, its position, sentiment and the reason, competitors named, citations (and how many are your own pages) and an excerpt. Use it when the user asks "what does ChatGPT say about us?" or why a prompt isn't naming them.

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
- **Trials** get one snapshot; it updates once they subscribe. Reading it is not metered, and never starts a build: `state: "not_built"` means the first snapshot hasn't been made yet (opening the AI Mentions page in the dashboard starts it), so say that rather than showing zeros.
