# /link-building

Link Building — earn backlinks from the pages AI already cites. Prospects are the specific **pages** AI quotes for your prompts (page-level, not just domains), each **quality-scored**, enriched with authority (DR), a **discovered contact**, and a ready-to-send pitch. You always send it yourself — nothing is auto-sent.

```bash
# List your prospects
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/link-building \
  | jq '{count, targets: [.targets[] | {motion, source_title, source_url, dr, quality_score, contact_email, status}]}'
```

> Previously `/get-cited`, and before that `/backlink-outreach`. If a user says either, they mean this. `/api/v1/get-cited` and `/api/v1/outreach` still 308 to `/api/v1/link-building`, so old keys keep working.

## Where prospects come from

Your AI-visibility citations. Pages **you own** and pages that **already list you** are filtered out. **Competitor pages are excluded too** — you can't earn a link on a rival's own domain; those live on AI Visibility → Citations as intelligence instead.

Each prospect carries a motion — how you'd win it:

- **`email`** — publisher listicles / roundups ("15 Best SEO Tools"). A drafted **email pitch** to the editor.
- **`engage`** / **`social`** — Reddit, Quora, LinkedIn, forum threads. A drafted **value-first reply**. Read the room before posting.
- **`review`** — G2 / Capterra / Trustpilot. A **claim link** + a **review-request** message for happy customers.

## Quality score — read this before you rank anything

`quality_score` (0-100) with a one-line `quality_reason` and `quality_flags[]`.

It is **weighted toward citation demand over raw DR** — how often AI actually quoted that page for the user's prompts predicts a win far better than domain authority does. Sites advertising paid placements are **disqualified outright** (capped near zero), not merely marked down. Only prospects above the quality floor get spent on enrichment.

**Rank by `quality_score`, not by `dr`.** A DR 40 page AI cites for four of your prompts beats a DR 80 page it cited once.

## Contacts are found, never guessed

`contact_email` comes from the byline on the exact page → the author's own page → team / write-for-us pages. **An address is never constructed** from a name-and-domain pattern.

- **`contact_confidence`** (0-100) and **`contact_basis`** (one sentence naming where it came from) — a score with provenance, not a checkmark. Quote the basis when you present a contact.
- **`contact_depth`** — `none` → `basic` → `deep`. **Only say "no email found" when `contact_depth` is `deep`.** At `none`/`basic` the honest line is "we haven't finished looking."
- `contact_name` / `contact_role` are worth surfacing even with no address — a named editor changes what the user does next.

## Verified links vs claimed status

`status` is what the **user said** happened. The `link_*` fields are what we **actually saw on the page**:

`link_live` (`null` = never checked, `false` = checked and absent), `link_anchor`, `link_rel`, `link_dofollow`, `link_first_seen`, `link_lost_at`.

When they disagree, **trust `link_live`** and say so plainly.

## Status flow

`prospect` → `drafted` → `contacted` → `replied` → `won` / `lost`

**`rejected` is separate** — the user looked and passed *without pitching*. Never fold it into `lost`; that would understate the reply rate.

`replied` rows also carry `reply_sentiment` (`interested` / `needs_work` / `declined`) and `reply_note`, logged by the user — we don't read anyone's inbox.

## Actions

```bash
# Find/refresh prospects from the latest AI-visibility run
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/link-building | jq '{ok, added, reason, message}'

# Update workflow — status, notes, or edit the draft/contact
curl -s -X PATCH -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"status":"contacted","contactEmail":"editor@example.com"}' \
  https://www.seoladders.com/api/v1/link-building/<TARGET_ID> | jq .

# Draft a short follow-up for a contacted target
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/link-building/<TARGET_ID>/followup | jq .
```

- **find** (POST) needs a completed AI-visibility run — prospects come from the sources AI cites for you. On `ok:false` with `reason` `no_monitor`/`no_run`, tell the user to **create and run an AI monitor first** (Dashboard → AI Visibility). Refreshing preserves existing workflow (status/notes/drafts).
- **update** (PATCH) accepts `status`, `notes`, `draftSubject`, `draftBody`, `followupSubject`, `followupBody`, `contactEmail`, `contactUrl`.
- **followup** (POST) is grounded in the first email. **Follow-ups cap at two** — check `followup_count` before suggesting another, and if it's already 2, the honest advice is to let it go.

## What to do with the result

- Lead with **`quality_score` + `quality_reason`**, then `motion` and the drafted `draft_subject`. The user reviews, edits, and **sends from their own inbox**.
- Never describe anything as auto-sent, and never present a guessed address as a contact.
- This is the "earn links" step that closes the gaps `/ai-visibility` surfaces (`topCitations` where `owned:false`). See `references/ai-visibility-playbook.md`.
