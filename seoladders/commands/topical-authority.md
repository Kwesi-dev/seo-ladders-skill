# /topical-authority [list|get|create|delete|research|add-keywords|remove-keyword|add-prompts|remove-prompt]

Build and track **topic clusters** — a pillar topic, the keywords that give it comprehensive coverage, **and the AI prompts (buyer questions) you track under it** to win that topic in AI answers. This is the same cluster you see under a topic in the dashboard: keywords for Google, prompts for AI visibility, all in one place.

Each keyword is scored:

- **covered** — a published article exists for it,
- **planned** — an article is queued or drafting (on the calendar / unpublished),
- **gap** — nothing written yet.

A topic returns two different numbers, and they answer different questions.

`coverage` (`{ total, covered, planned, gap, coveragePct }`) is what was **written** for the topic, and finds what's left to write.

`authority` (`{ authority, searchScore, aiScore, stage, evidence }`) is what the topic has **won**: of the articles SEOLadders published for it, how many now rank, plus the share of its tracked prompts whose AI answers name the brand. `stage` runs `planned → published → indexed → ranking → winning → cited` and stays meaningful while a young topic's score is honestly near zero. Pages that existed before the account was created are excluded by construction, so this only ever credits work the platform did.

Coverage can read 100% while authority reads 0 — that means everything planned was written and none of it ranks yet. Base URL `https://www.seoladders.com/api/v1`. Auth header on every call: `Authorization: Bearer $SEO_LADDERS_API_KEY`.

## List topics

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics \
  | jq '{count, topics: [.topics[] | {id, name, coverage, authority}]}'
```

`topics` — `[{ id, name, description, pillar_post_id, coverage: {total, covered, planned, gap, coveragePct}, authority: {authority, searchScore, aiScore, stage, evidence} }]`.

## Get one topic (keywords + prompts + coverage + authority)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID \
  | jq '{name: .topic.name, coverage, authority, keywords: [.keywords[] | {id, keyword, coverage, searchVolume, keywordDifficulty, intent}], prompts}'
```

- `keywords[]` each carry `coverage` (covered / planned / gap) — the **gaps** are your to-write list.
- `prompts` — `[{ id, text, intent, isActive }]`, the AI prompts tracked under this topic (see below).

## Create a topic

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"technical seo","description":"crawling, indexing, site health"}' \
  https://www.seoladders.com/api/v1/topics | jq '.topic'
```

`name` is required. `description` is optional.

## Research keywords for a topic (metered)

Pull real DataForSEO candidates (keyword ideas + related + suggestions, merged and relevance-filtered) for the topic. Candidates are **not saved** — review them, then add the ones you want.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/research \
  | jq '{count, candidates: [.candidates[] | {keyword, searchVolume, keywordDifficulty, intent}]}'
```

- No body needed — it seeds from the topic name and your primary location.
- **Metered** against your keyword-research quota (same pool as `/keyword-research`) — returns **HTTP 429** when spent. Call it once per topic; don't loop it.
- Never returns more than the free slots under the **30-keyword-per-topic** cap.

## Add keywords to a topic

```bash
# Plain strings…
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keywords":["seo crawl budget","xml sitemap best practices"]}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/keywords | jq '{count, added: [.added[] | {id, keyword}]}'

# …or objects with metrics (e.g. straight from /research)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keywords":[{"keyword":"seo crawl budget","searchVolume":1300,"keywordDifficulty":42,"intent":"informational"}]}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/keywords | jq '.added'
```

De-duplicates against the existing map and respects the 30-per-topic cap (only free slots are filled).

## Remove a keyword

```bash
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/keywords/KEYWORD_ID | jq .
```

`KEYWORD_ID` is the `id` from `keywords[]` in the get response.

## Track AI prompts under a topic

Prompts (buyer questions you track across ChatGPT/Perplexity/Gemini/Claude/Google AI) belong to a topic — that's how the same cluster covers both Google (keywords) and AI answers (prompts). List, add, and remove them per topic.

```bash
# List a topic's prompts
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/prompts \
  | jq '{count, prompts: [.prompts[] | {id, text, intent, isActive}]}'

# Add prompts under the topic (intent optional — defaults to "category")
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompts":["best tool for technical seo","how do I fix crawl budget"],"intent":"category"}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/prompts \
  | jq '{count, added: [.added[] | {id, text, intent}], skipped}'
```

- **Needs AI Visibility set up** — a monitor must exist, or the add returns HTTP 409 with a message. Set it up in the dashboard first.
- `intent` is one of `category | comparison | problem | evaluative | organic | brand_specific | competitor_comparison`.
- Cap-aware — over-cap prompts come back in `skipped` (the cap is the plan's AI-prompt budget, shared with `/prompts`).

```bash
# Stop tracking a prompt under the topic
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/prompts/PROMPT_ID | jq .
```

`PROMPT_ID` is the `id` from `prompts[]` in the get response. To find prompts worth adding, use `/prompts` (suggestions from GSC, your keywords, or People-Also-Asked), then add the good ones here under the right topic.

## Update / delete a topic

```bash
# Rename or set the pillar page
curl -s -X PATCH -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"name":"technical SEO"}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID | jq '.topic'

# Delete the topic (and its keywords)
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID | jq .
```

## The workflow

1. **Create** a pillar topic (or list what you already have).
2. **Research** to pull candidate keywords, then **add** the strong ones — build the map out toward comprehensive coverage (no DR filtering; the goal is to own the topic).
3. **Track prompts** under the topic — the buyer questions you want AI to name you for. Pull suggestions with `/prompts`, then add the good ones to the topic. This is the GEO half of the cluster.
4. **Get** the topic and read `coverage` — the **gap** keywords are what's left to write, and `prompts` are what AI visibility is tracking for it.
5. For each gap keyword, hand it to **`/write-article`** to publish content (that flips it planned → covered).
6. Re-check `coveragePct` to see the map fill in, and `authority` to see whether it is actually winning. Coverage moves the week an article publishes; authority moves months later, when that article starts ranking and getting cited.
