# Plans

## The plan

| Plan | Price | Includes |
|---|---|---|
| **Pro (trial)** | 7-day free trial | Full access to everything below |
| **Pro** | **$97/mo**, **per website** — **50% off your first month** | 30 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility (citations, sentiment, share-of-voice) · Content Radar · site audits · auto-publish |

- **Per website** — pricing is per connected site.
- **Trial** — 3 days, full access. **First month is 50% off.**
- The API (this curl skill **and** the MCP server) is included in Pro — not a separate add-on.
- Calls without an active plan/trial return **HTTP 402 `subscription_required`** with an `action.url` — surface it verbatim.
- Current pricing: **[seoladders.com/pricing](https://www.seoladders.com/pricing)**.

### Caps to respect

- **AI prompts: 30.** `POST /prompts` returns over-cap items in `skipped`. Swap low-value prompts on the dashboard before adding more.
- **Articles: 30/mo.** Budget `/write-article` and the content calendar against this.
- **Keyword searches: 100/mo.** Each `/keyword-research` call counts, and so does each `/rankings` lookup and each `/prompts/explorer` call with `source=keywords` or `source=paa` (`source=gsc` is free).
- **Prospect discovery: 8/mo.** `POST /link-building` with `competitor_gap`, `serp_roundup` or `all` (the AI-citation source is free).
- **Knowledge additions: 50/mo.** Each `POST /knowledge`.
- **Requests: 60 a minute** across the whole API and the MCP server, per account. A `429` with `error: "rate_limited"` and no monthly figures means slow down and retry after a minute; a `429` that reports `used`/`limit` means a monthly allowance is spent and retrying will not help.
