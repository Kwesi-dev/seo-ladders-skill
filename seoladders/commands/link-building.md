# /link-building

Link Building — earn backlinks from the pages that decide whether you get considered. Prospects are specific **pages** (page-level, not just domains) found two ways: pages **AI quotes** when answering your prompts, and pages that **link to your competitors and not to you**. Each is **quality-scored**, enriched with authority (DR), a **discovered contact**, and a ready-to-send pitch. You always send it yourself — nothing is auto-sent.

```bash
# List your prospects
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/link-building \
  | jq '{count, targets: [.targets[] | {motion, source_title, source_url, dr, quality_score, contact_email, status}]}'
```

> Previously `/get-cited`, and before that `/backlink-outreach`. If a user says either, they mean this. `/api/v1/get-cited` and `/api/v1/outreach` still 308 to `/api/v1/link-building`, so old keys keep working.

## Where prospects come from

**`discovery_source` tells you which of two, and it changes what you say about the prospect.**

**`ai_citation`** — pages AI quoted when answering your tracked prompts. The evidence is `citation_count`: an assistant reached for this page N times and you weren't in the answer.

**`competitor_gap`** — pages that link to your competitors and not to you, from the backlink index rather than any AI answer. The evidence is `competitors_listed[]` (which rivals appear on that exact page) and how many. **`citation_count` is 0 on every one of these and does not mean "unpopular"** — it means AI is not the reason we found it. Reporting a gap prospect as having "no citations" is reporting a fact about our discovery method as if it were a fact about the page.

`demand` is the **shared** signal both write to — citation count for AI rows, competitor count for gap rows. It is the only field that is comparable across sources.

Filtered out either way: pages **you own**, pages that **already list you**, and **competitors' own domains** (you can't earn a link on a rival's site; those live on AI Visibility → Citations as intelligence instead). Gap prospects are additionally screened for syndication networks, scrapers, spam score and dead pages before they ever reach the list.

Each prospect carries a motion — how you'd win it:

- **`email`** — publisher listicles / roundups ("15 Best SEO Tools"). A drafted **email pitch** to the editor.
- **`engage`** / **`social`** — Reddit, Quora, LinkedIn, forum threads. A drafted **value-first reply**. Read the room before posting.
- **`review`** — G2 / Capterra / Trustpilot. A **claim link** + a **review-request** message for happy customers.
- **`contribute`** — a curated list in a git repo (GitHub awesome-lists and similar). **No editor, no inbox: it takes a pull request.** Read CONTRIBUTING.md, match the surrounding entries exactly, open a PR with a one-line description and no marketing copy. Frequently the highest-authority prospect on the whole list.
- **`submit`** — directories that accept submissions (SaaSHub, AlternativeTo, Product Hunt and the like). Every competitor listed there submitted themselves, so **there is nothing to pitch** — find the "add your product" form and fill it in properly.

**`contribute` and `submit` never get a drafted email and never appear in Ready.** That is correct behaviour, not a missing contact: there is no address that would help. If a user asks why a GitHub prospect has no draft, that is the answer.

## Quality score — read this before you rank anything

`quality_score` (0-100) with a one-line `quality_reason` and `quality_flags[]`.

It is **weighted toward citation demand over raw DR** — how often AI actually quoted that page for the user's prompts predicts a win far better than domain authority does. Sites advertising paid placements are **disqualified outright** (capped near zero), not merely marked down.

**Rank by `demand` first, then `quality_score`, and only then `dr`.**

That order matters, and the reason is a data fact rather than a preference: `quality_score` and `dr` are only computed once a prospect has been enriched, so **most rows have neither**. Sorting by `quality_score` silently buries every unenriched prospect — including high-DR pages cited five times, which are among the best targets on the list. `demand` is present on every row and is the signal the other two are proxies for.

**Do not rank on `citation_count`.** It was the right field when every prospect came from an AI answer; now it is 0 on every `competitor_gap` row, so sorting by it puts all of them below all of the AI ones permanently — the gap prospects would be invisible while appearing to be in the list. `demand` exists precisely to be the one comparable number.

Treat a null `quality_score` as *unmeasured*, never as *bad*.

## Contacts are found, never guessed

`contact_email` is found by working outward from the page you want a link on: the byline on that exact page → the author's own page → team / write-for-us pages the site actually publishes (discovered, not guessed at) → the open web → a contact database, and only ever for someone already confirmed to work there. **An address is never constructed** from a name-and-domain pattern.

- **`contact_confidence`** (0-100) and **`contact_basis`** (one sentence naming where it came from) — a score with provenance, not a checkmark. Quote the basis when you present a contact.
- **`contact_depth`** — `none` → `basic` → `deep` → `research`. **Only say "no email found" when `contact_depth` is `research`.** Anything earlier means we haven't finished looking, and saying otherwise reports a gap that may not exist.
- **`contact_route`** — how to actually reach them, and more important than the address itself:

| Route | What it means |
|---|---|
| `personal_email` | you have the individual |
| `generic_inbox_named` | a shared inbox, **but we know whose name to open with** |
| `generic_inbox` | the same shared address with nobody's name — the weakest live route |
| `profile_only` | a named human, no address published |
| `none` | nothing found |

`generic_inbox` and `generic_inbox_named` carry the **identical address** and are completely different prospects. Never present a shared inbox as a personal contact, and when you have the named variant, lead with the name — that is the entire difference between a 2% and a 12% reply rate.
- `contact_name` / `contact_role` are worth surfacing even with no address — a named editor changes what the user does next.

## Verified links vs claimed status

`status` is what the **user said** happened. The `link_*` fields are what we **actually saw on the page**:

`link_live` (`null` = never checked, `false` = checked and absent), `link_anchor`, `link_rel`, `link_dofollow`, `link_first_seen`, `link_lost_at`.

When they disagree, **trust `link_live`** and say so plainly.

## Status flow

`prospect` → `drafted` → `contacted` → `replied` → `won` / `lost`

**`rejected` is separate** — the user looked and passed *without pitching*. Never fold it into `lost`; that would understate the reply rate.

`replied` rows also carry `reply_sentiment` (`interested` / `needs_work` / `declined`) and `reply_note`, logged by the user — we don't read anyone's inbox.

## Contacts arrive on a schedule, not on demand

**Ten a week, every Monday** — five from AI citations and five from the competitor gap, with the pitches drafted and waiting. There is no per-prospect "find email" action, and asking for one is not a thing the API exposes.

The five-and-five split is reserved seating, not a coincidence of ranking. There are far more AI prospects than gap ones and `demand` means different things on each side, so a single ranked pool would let whichever scale ran hotter take every seat. Each source gets its own quota so both motions stay alive.

This is deliberate. A prospect list runs to hundreds of pages; nobody sends hundreds of pitches. Finding every address would spend on the ones that will never be written to and leave a wall of drafts going stale — a pitch referring to someone's "recent" article about something published two months ago does more harm than not writing at all.

Two consequences worth telling a user plainly:

- **Most rows will have no contact, and that is not a failure.** `contact_researched_at` being null means we haven't reached that prospect yet, not that it has no contact. Don't report those as misses.
- **The queue backs off.** If unsent drafts are already piling up, the batch finds fewer or skips entirely, rather than adding to a queue the user isn't working through.

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

- **find** (POST) refreshes the AI-citation prospects and needs a completed AI-visibility run. **The competitor gap is not on this endpoint** — it refreshes inside the Monday job, from the competitors on the product, and there is no way to trigger it by API. Don't tell a user to POST to get their gap prospects. On `ok:false` with `reason` `no_monitor`/`no_run`, tell the user to **create and run an AI monitor first** (Dashboard → AI Visibility). Refreshing preserves existing workflow (status/notes/drafts).
- **update** (PATCH) accepts `status`, `notes`, `draftSubject`, `draftBody`, `followupSubject`, `followupBody`, `contactEmail`, `contactUrl`.
- **followup** (POST) is grounded in the first email. **Follow-ups cap at two** — check `followup_count` before suggesting another, and if it's already 2, the honest advice is to let it go.

## What to do with the result

- Lead with **`quality_score` + `quality_reason`**, then `motion` and the drafted `draft_subject`. The user reviews, edits, and **sends from their own inbox**.
- Never describe anything as auto-sent, and never present a guessed address as a contact.
- **Say where a prospect came from.** "AI cited this page 5 times and you're not in it" and "this page links to 3 of your competitors and not you" are different arguments, and the second one is not weaker for having no citations.
- This is the "earn links" step that closes the gaps `/ai-visibility` surfaces (`topCitations` where `owned:false`). See `references/ai-visibility-playbook.md`.
