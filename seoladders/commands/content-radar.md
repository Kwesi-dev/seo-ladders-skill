# /content-radar

Pull every article from Google Search Console, flag what's dead, declining, competing, out of date, stuck or buried, and route each to a fix. Articles only. Needs GSC.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar \
  | jq '{connected, count, rows: [.rows[] | {url, primaryKeyword, position, impressions, clicks, ctr, bucket, action, positionDrop, trend: (.history // [] | map(.position))}]}'
```

## How to read it

- **`hasHistory`** / **`pagesSeen`** — top-level. They separate "we have not gathered data yet" from "nothing needs attention": empty `rows` with `pagesSeen: 0` is the former, and calling the site healthy in that case is wrong.
- **`connected`** — `false` means GSC isn't connected. Tell the user to connect Google Search Console at the dashboard; without it Content Radar has nothing to read.
- `rows` — `[{url, primaryKeyword, position, impressions, clicks, ctr, source, blogPostId, bucket, action, reason, positionDrop?, history?, staleness?, underLinked?, competingWith?, maturityMonths?, maturitySource?}]`. `reason` is the one-line explanation of why the row was flagged — quote it rather than inventing your own. `history` is `[{date, position}]` — the page's weekly avg-position trend (lower = better), so you can see whether it's improving or slipping over time.
- **`bucket`** — listed worst-first, which is also the order to work them:
  - `dead_with_demand` — the URL returns 404/410 but Google **still shows it** and it's still earning impressions. Traffic already won, landing on an error page. Highest priority: everything else is an improvement, this one is a leak.
  - `declining` — was ranking higher and has slipped for weeks without recovering (see `positionDrop`).
  - `striking_distance` — **stuck** around positions 5–20 and no longer climbing. Pages still moving up on their own are deliberately excluded — they're already fixing themselves.
  - `low_ctr` — ranks on page 1 but earns **under half the clicks normal for its position**. A title/meta problem, not a content one. (Position-relative, not a flat CTR threshold — 2% is fine at #9 and terrible at #2.)
  - `page_two_plus` — on page 3 or 4 (positions 21 to 40) and never reached page 1. The dashboard labels it "Page 3 or 4".
  - `stale` ("Needs updating") — no GSC symptom yet, but the article has gone out of date: an old year in the title or an H1 to H3 heading (never the body), or outbound links that are really dead (404/410, real server errors, DNS failures; a site blocking bots, such as LinkedIn's 999, does not count). Caught before the ranking slips.
  - `cannibalization` ("Competing pages") — two of *their own* articles are splitting one query. The row carries the weaker contender and `competingWith` names the other. Rewriting either in isolation does not fix it; the fix is merge or differentiate. Only reported once, over the last 90 days, both have at least 25 impressions for the query inside the top 50 and the weaker earns at least 15% of the stronger's impressions.
  - `underperforming` — buried past position 40 for 6+ months. **Not an optimize candidate** — a rewrite rarely rescues a page this deep; the fix is usually merge or prune.
- **`action`** — the verdict:
  - `refresh` — the verdict for `declining` and `stale` rows: a page that already ranks and needs bringing up to date rather than rebuilding. `blogPostId`-only, so it applies to pages *we* generated. Do not send these to `/optimize`.
  - `optimize` — an article to rewrite from GSC data (`/optimize`). Covers articles *we* generated (by `blogPostId`) **and pre-join articles written before joining** (by the page `url`). For a `url` page nothing is refreshed in place — we hold no copy of it, so the result is a draft to hand to the user. See the warning in `/optimize`.
  - `write` — a dead **article** URL worth rebuilding at the *same* URL, so the impressions Google already holds carry over. Only appears when nothing else of theirs covers the query. **This never happens on its own:** automatic dead-URL rebuilds are switched off, so the row is a suggestion and the rebuild is a button the user presses (it bills article generation, not the optimize allowance).
  - `recommend` — advice rather than a rewrite. Covers **dead articles whose content moved** (redirect decision), **buried articles** (merge/prune decision) and **competing articles** (which one owns the query) → fetch the checklist or fix plan below.
- **`redirectTo`** — dead rows only. When present, a live page of theirs already ranks for that query, so the page **moved** rather than died: redirect to it. Never write a replacement in that case — you'd put two of their own pages against one query.
- **`source`** — `internal` (we generated it) vs `external` (pre-join / not ours).

> **Articles only.** Content Radar lists blog posts and articles, nothing else. The homepage, pricing, tool, feature, docs, legal and section index pages (`/blog`, `/tools`, `/features`) are never on it, because every fix it offers (refresh, rewrite, merge, prune) is a content fix. If a user asks why their pricing page isn't listed, that's why; it's not a bug. Technical and on-page problems on those pages come from the site audit (`/gsc-audit`), and a checklist for any single URL from the recommendations call below.

> **Waiting periods.** An article is only judged once it has had time to settle, so nothing is flagged while it is still climbing:
> - `declining`, `low_ctr`, `striking_distance`, `stale`: about 3 months.
> - `page_two_plus`, `underperforming`: about 6 months.
> - `dead_with_demand` and `cannibalization`: straight away. A 404 that Google still shows has no settling period, and a competing pair is only reported once both pages already hold real impressions.
>
> Age is the publish date for articles we wrote, and how far back Search Console has data for the URL otherwise (`maturitySource`: `published` or `observed`).

## Getting recommendations for one page

For rows with `action: "recommend"`, fetch a concrete on-page checklist. This call takes any URL on the site, including a pricing or feature page that is not on the radar:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/pricing","keyword":"best crm pricing","bucket":"page_two_plus","position":24}' \
  https://www.seoladders.com/api/v1/content-radar/recommendations | jq '.recommendations[]'
```

Returns `{ recommendations: [{ title, detail, priority }], cached }`. A saved checklist (yours or the dashboard's) comes back free; `refresh: true` builds a new one. At most 15 fresh checklists a day per site. Pass the row's `url`, `keyword`, `bucket`, and `position` — **the checklist changes with the bucket**:

- most buckets → on-page fixes (title/meta, headings, content gaps, internal links, schema);
- `dead_with_demand` → starts with the rebuild-vs-redirect decision, and never suggests editing a page that doesn't exist;
- `underperforming` → starts with merge / prune / rebuild, and deliberately does **not** suggest title tweaks, which don't move a page from #70.

## Fixing one article: the full diagnosis and work order

For a row on the radar that you can't rewrite through SEOLadders (a buried or
competing article, or one that lives in the user's own codebase), ask for the fix plan:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/blog/crm-onboarding-guide"}' \
  https://www.seoladders.com/api/v1/content-radar/fix-plan | jq .
```

Returns `{ plan: { url, keyword, diagnosis, items, publishable, generatedAt }, markdown, cached }`.

- Pass the `url` exactly as the radar reports it. `keyword` is optional and defaults to the row's own primary query, so don't guess one.
- Only URLs on the radar get a plan: the plan is built from the row's own position, trend and bucket. Any other URL, a pricing page included, comes back with an `error` saying it is not on the Content Radar. Use the recommendations call above for those.
- `diagnosis` says why it's losing (position, click-through against what that position normally earns, the trend, missing schema).
- `items` are the fixes: `replace` items are finished strings ready to paste (title, meta description, H1, FAQ, JSON-LD); `spec` items are requirements for copy that must be written inside the site's own components (the opening, heading outline, coverage gaps). Never invent that prose yourself.
- `markdown` is a complete work order with acceptance criteria: the form to hand a coding agent.
- If the URL redirects or 404s you get an `error` explaining that instead of a plan. That's a real finding (Search Console reports moved URLs for months), not a failure to retry.
- Takes about 20 seconds and is cached per page. Pass `"refresh": true` only after the fixes are applied; a plan less than 24 hours old comes back from cache even then.
- **Nothing is published.** We have no write path to these pages, so the plan is for the user or their agent to apply. Over MCP this is the `content-radar-fix-plan` tool. At most 10 fresh plans a day per site (cached plans are free); a `429 daily_cap_reached` resets at 00:00 UTC.

## What to do with the result

- `refresh` → a dated page we wrote; bring it up to date (not `/optimize`). `optimize` → `/optimize`. `write` → offer the rebuild; nothing queues itself. `recommend` → fetch the checklist above and walk the user through the fixes.
- **Nothing on this list rewrites itself.** Unattended rewriting of published pages is switched off by design — Content Radar finds and routes the work, a human chooses what runs. Never tell the user a flagged page will fix itself.
- Priority order, worst first: `dead_with_demand`, `declining` (largest `positionDrop`), `stale`, `cannibalization`, `striking_distance`, `low_ctr`, `page_two_plus`, `underperforming`.
- Do **not** send `underperforming` rows to `/optimize`. Positions 5–20 are where refresh work pays; past 40 the evidence favours consolidating or pruning instead.
- This is the audit-first flow. See `references/audit-playbook.md`.
