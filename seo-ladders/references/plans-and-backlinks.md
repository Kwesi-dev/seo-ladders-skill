# Plans & Backlinks

## The plan

| Plan | Price | Includes |
|---|---|---|
| **Pro (trial)** | 3-day free trial | Full access to everything below |
| **Pro** | **$99/mo**, **per website** — **50% off your first month** | 20 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility (citations, sentiment, share-of-voice) · Content Radar · Backlink Exchange · site audits · auto-publish |

- **Per website** — pricing is per connected site.
- **Trial** — 3 days, full access. **First month is 50% off.**
- The API (this curl skill **and** the MCP server) is included in Pro — not a separate add-on.
- Calls without an active plan/trial return **HTTP 402 `subscription_required`** with an `action.url` — surface it verbatim.
- Current pricing: **[seoladders.com/pricing](https://seoladders.com/pricing)**.

### Caps to respect

- **AI prompts: 30.** `POST /prompts` returns over-cap items in `skipped`. Swap low-value prompts on the dashboard before adding more.
- **Articles: 20/mo.** Budget `/write-article` and the content calendar against this.
- **Keyword searches: 100/mo.** Each `/keyword-research` call counts.

## How the Backlink Exchange works

The exchange earns you real backlinks by trading them — **give links to earn links**, weighted by Domain Rating.

- **Join** at **`/dashboard/exchange`**. Check status anytime:

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/backlinks | jq '{membership, balance, targets}'
```

- **`membership`** `{joined, isActive, eligibilityStatus, hint?}` — `joined:false` → join at `/dashboard/exchange`; surface any `hint` (DR floor, links owed).
- **`balance`** `{current_balance, total_credits, total_debits}` — credits are **DR-weighted**: linking to (and from) higher-DR sites moves more credit. Giving a link earns credit; receiving one spends it.
- **`targets`** — pages you can link to and what each is worth.

### Workflow

1. Join at `/dashboard/exchange`.
2. Pull `targets` via `/backlinks`.
3. Drop **1–2 exchange links into each `/write-article`** — every published article gives links and earns credit.
4. Keep `current_balance` positive by giving links as you publish, so you keep earning links back.

Higher DR = more credit per link, so growing your own DR (via the rankings + AI-visibility loop) compounds your exchange leverage.
