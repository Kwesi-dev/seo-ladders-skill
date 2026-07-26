# /get-cited

Get Cited — earn mentions in the sources AI already trusts. Targets are the specific **pages** AI cites for your prompts (page-level, not just domains), each classified by how you'd win it, enriched with authority (DR) and a ready-to-send asset. You always send it yourself — nothing is auto-sent.

```bash
# List your Get Cited targets
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/get-cited | jq '{count, targets: [.targets[] | {motion, source_title, source_url, dr, status}]}'
```

## The four motions

Each cited source gets you in a different way, so each carries its own drafted move:

- **`publisher`** — listicles / roundups (e.g. "15 Best SEO Tools"). A drafted **email pitch** to the editor to be added.
- **`community`** — Reddit / Quora / forum threads. A drafted **value-first reply** that mentions you naturally. Read the room and post.
- **`review`** — G2 / Capterra / Trustpilot. A **claim link** + a **review-request** message for happy customers.
- **`video`** — YouTube videos AI leans on. A **creator pitch** to feature or update with you.

Your own pages, competitors, and pages that already list you are filtered out.

## How to read it

- `targets` — `[{ motion, source_url, source_title, dr, citation_count, contact_email, contact_url, draft_subject, draft_body, status }]`.
- **`status`** — `prospect` → `drafted` → `contacted` → `replied` → `won` / `lost`.
- **`dr`** — domain rating of the source (higher = more authority). **`citation_count`** — how often AI cited it for your prompts.

## Actions

```bash
# Find/refresh targets from the latest AI-visibility run (needs a completed run)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/get-cited | jq '{ok, added, reason, message}'

# Update a target's workflow — status, notes, or edit the draft/contact
curl -s -X PATCH -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"status":"contacted","contactEmail":"editor@example.com"}' \
  https://www.seoladders.com/api/v1/get-cited/<TARGET_ID> | jq .

# Draft a short follow-up for a contacted target
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/get-cited/<TARGET_ID>/followup | jq .
```

- **`find`** (POST) needs a completed AI-visibility run — targets come from the sources AI cites for you. If it returns `ok:false` with `reason` `no_monitor`/`no_run`, tell the user to **create and run an AI monitor first** (Dashboard → AI Visibility). It preserves existing workflow (status/notes) on refresh.
- **`update`** (PATCH) accepts `status`, `notes`, `draftSubject`, `draftBody`, `contactEmail`, `contactUrl`.

## What to do with the result

- Present the top targets by `dr` / `citation_count` with their `motion` and the drafted `draft_subject` — the user reviews, edits if they like, and **sends from their own inbox**.
- Never describe anything as auto-sent. The asset is drafted; the user always sends.
- This is the "earn citations" step that closes the gaps `/ai-visibility` surfaces (`topCitations` where `owned:false`). See `references/ai-visibility-playbook.md`.
