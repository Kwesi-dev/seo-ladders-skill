# /gsc-audit <domain>

Full SEO audit — health score, CTR, decay, page-2, technical issues. Async (returns a `jobId`), or read the latest stored audit.

**Domain** = `$ARGUMENTS`. If `$ARGUMENTS` is empty, send `{}` to audit your active site.

## Run a new audit (async)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"domain":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/audit | jq '{jobId, status}'
```

`domain` is optional — omit it (or send `{}`) to audit your active site.

## Poll the job until done

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/jobs/JOB_ID | jq '{status, output, error}'
```

- Poll every ~5–10s until `status` is `completed` (or `failed`).
- On `completed`, read `output` for the audit. On `failed`, surface `error`.

## Or read the latest stored audit (no wait)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit | jq '.audit'
```

Returns `{ audit: {id, status, health_score, total_issues, total_pages_crawled, ...} | null }`. `null` → no audit yet, run one.

## What to do with the result

- Report `health_score`, `total_issues`, `total_pages_crawled`.
- Audit **before** writing anything. Pair with `/content-radar` to route pages to refresh vs optimize. See `references/audit-playbook.md`.
