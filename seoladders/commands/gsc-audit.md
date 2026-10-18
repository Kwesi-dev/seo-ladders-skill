# /gsc-audit

Your site audit: health score and every open issue, worst first, with the page, why it matters and what to do.

**The agent already runs it.** It audits your site when the trial starts and again every Monday on its own, so there is almost always a recent one. Read it; don't start one.

## Read the audit the agent already ran (free)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit \
  | jq '{audit: .audit | {health_score, total_issues, total_pages_crawled, created_at}, bySeverity: .report.bySeverity, issues: .report.issues[:10], running: .report.running, dashboardUrl: .report.dashboardUrl}'
```

(MCP: `gsc-audit` with no args.)

- `report.issues[]`: severity, category, title, page, `whyItMatters`, `whatToDo`, current and suggested value, pages affected. Up to 60, worst and most widespread first.
- `report.running`: an audit in progress right now, if any.
- `report.dashboardUrl`: the full report with every page.
- `audit: null` and nothing running → the first audit hasn't run yet (it starts when onboarding finishes).

## If the user asks for a new audit

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/audit | jq '{auditId, status, alreadyRunning, alreadyAudited, message, dashboardUrl}'
```

(MCP: `gsc-audit` with `run: true`.)

- If an audit is **running**, you get it back (`alreadyRunning`), not a second one.
- If one **finished in the last 7 days**, you get it back with its report (`alreadyAudited`). Nothing is spent.
- Only when the user has **fixed things since** and wants to see it now, send `{"force": true}`. That crawl is metered against the plan's site audits and billed per page, so confirm first.
- Poll a running audit with `GET /api/v1/audit?id=AUDIT_ID` until `status` is `ready`.
- Always your own site.

## What to do with the result

- Lead with what the agent did: "Your agent audited your site on [date]: health 82, 4 high-priority issues." Then the high ones with `whatToDo`, and the dashboard link for the rest.
- Mention it re-audits every Monday, so fixes show up there without asking.
- Pair with `/content-radar` for pages losing traffic. See `references/audit-playbook.md`.
- When the user marks an issue fixed in the dashboard, the page is **re-checked live**; a fix that did not land comes back as `not_verified` rather than silently closing.
