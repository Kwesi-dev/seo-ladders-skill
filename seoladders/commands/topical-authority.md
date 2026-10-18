# /topical-authority [list|get]

Your **topic clusters**, which the agent built for you: each pillar topic, the keywords that give it comprehensive coverage, **and the AI prompts (buyer questions) it tracks under it** to win that topic in AI answers. The same clusters are on the dashboard at /dashboard/topics.

**Read-only here.** The agent researched and built these from your site, competitors and Search Console, so this command shows them; nothing is created or deleted from chat. To change a cluster, use the dashboard.

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

## What to do with the result

1. **Lead with what the agent built**: "Your agent organised your market into 6 topic clusters, 48 keywords and 24 tracked buyer questions." Then list the topics with their stage.
2. On request, **get** one topic and show its keywords (covered, planned, gap) and the prompts tracked under it.
3. `gap` keywords are already on their way: the agent schedules them on the calendar and writes them. `/my-keywords` shows every keyword with its scheduled date in one call.
4. Read `coveragePct` for how much is written and `authority` for whether it is winning. Coverage moves the week an article publishes; authority moves months later, when it starts ranking and getting cited.
5. Link /dashboard/topics for the full map and for any change the user wants to make.
