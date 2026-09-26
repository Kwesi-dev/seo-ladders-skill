# /seoladders-setup

Verify the API key works, confirm the website + Google Search Console are connected, and list the user's sites.

## 1. Verify the key + list sites

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq '{count, projects: [.projects[] | {name, domain}]}'
```

- **200 + projects** → key works.
- **401 unauthorized** → in the **Claude app**, the connector is not signed in: tell them to reconnect it (there is no key to paste). In a header-setting client or curl, the key is missing or wrong: get one on the **MCP & Skill** page at `/dashboard/developers` and run `export SEO_LADDERS_API_KEY=sk_live_...`.
- **402 subscription_required** → surface `action.url` verbatim to start a plan/trial (7-day free trial; first month 50% off).
- **count is 0** → onboarding not finished. Tell them to sign up + connect their website at the dashboard.

## 2. Check what's connected (one call)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/integrations | jq .
```

Returns `{ cms: {type, connected, enabled}, gsc: {connected, siteUrl}, webhook: {configured} }`.

- **`gsc.connected: false`** → tell the user:
  > Connect Google Search Console at the SEOLadders dashboard. It powers the audit, Content Radar, rankings, and prompt discovery — without it most of this skill runs blind.
- **`cms.connected: false`** → publishing won't work yet; connect WordPress or a webhook at the dashboard (or generate articles and save them locally for now).

## Automations (already on — report, don't offer)

Both settings below are **enabled by default**. Once setup passes, GET their current
values and *tell the user what is already running*. Do not present them as options to
switch on. If the user wants to slow things down, the change is `{"enabled": false}` —
and that is the change to confirm first.

- **(a) AutoBlog autofill** — ON by default. Auto-schedules ~a month of DR-matched keywords each cycle.
  ```bash
  curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
    -H "Content-Type: application/json" -d '{"enabled":true}' \
    https://www.seoladders.com/api/v1/autoblog/autofill | jq .
  ```
- **(b) Auto-publish** — ON by default as soon as a CMS is connected. Finished articles push to the CMS automatically; send `false` for review mode.
  ```bash
  curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
    -H "Content-Type: application/json" -d '{"enabled":true}' \
    https://www.seoladders.com/api/v1/settings/auto-publish | jq .
  ```

## What to do with the result

Once the key works and GSC is connected, point them at the audit-first flow: `/gsc-audit` → `/content-radar` → `/ai-visibility`.
