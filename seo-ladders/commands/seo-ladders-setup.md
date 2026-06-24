# /seo-ladders-setup

Verify the API key works, confirm the website + Google Search Console are connected, and list the user's sites.

## 1. Verify the key + list sites

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq '{count, projects: [.projects[] | {name, domain}]}'
```

- **200 + projects** → key works.
- **401 unauthorized** → key is missing or wrong. Tell the user to get one at **Dashboard → Developers** and run `export SEO_LADDERS_API_KEY=sk_live_...`.
- **402 subscription_required** → surface `action.url` verbatim to start a plan/trial (3-day free trial; first month 50% off).
- **count is 0** → onboarding not finished. Tell them to sign up + connect their website at the dashboard.

## 2. Check what's connected (one call)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/integrations | jq .
```

Returns `{ cms: {type, connected, enabled}, gsc: {connected, siteUrl}, webhook: {configured} }`.

- **`gsc.connected: false`** → tell the user:
  > Connect Google Search Console at the SEO Ladders dashboard. It powers the audit, Content Radar, rankings, and prompt discovery — without it most of this skill runs blind.
- **`cms.connected: false`** → publishing won't work yet; connect WordPress or a webhook at the dashboard (or generate articles and save them locally for now).

## Offer automations (opt-in)

Once setup passes, **suggest** these three options — do **not** enable any of them. Act only on an explicit "yes" from the user; each is consequential (spends quota, publishes live, or enrolls in a network).

- **(a) AutoBlog autofill** — auto-schedules ~a month of DR-matched keywords each cycle.
  ```bash
  curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
    -H "Content-Type: application/json" -d '{"enabled":true}' \
    https://www.seoladders.com/api/v1/autoblog/autofill | jq .
  ```
- **(b) Auto-publish** — finished articles push to the connected CMS automatically (needs a CMS).
  ```bash
  curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
    -H "Content-Type: application/json" -d '{"enabled":true}' \
    https://www.seoladders.com/api/v1/settings/auto-publish | jq .
  ```
- **(c) Join the Backlink Exchange** — enroll in the DR-weighted network (link out, earn links in).
  ```bash
  curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
    -H "Content-Type: application/json" -d '{}' \
    https://www.seoladders.com/api/v1/backlinks/join | jq '{joined, membership}'
  ```

## What to do with the result

Once the key works and GSC is connected, point them at the audit-first flow: `/gsc-audit` → `/content-radar` → `/ai-visibility`.
