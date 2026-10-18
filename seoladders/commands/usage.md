# /usage

What's left of this billing cycle's allowances: the same ones the dashboard draws on. Free.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/usage | jq '{tier, cycle, surfaces: [.surfaces[] | {label, used, limit, remaining}], standing}'
```

(MCP: `usage`.)

- **`surfaces`**: articles, keyword research, competitor gap, site audits, optimizations, AI answers, rank checks, link discovery, knowledge additions, each with used / limit / remaining.
- **`standing`**: tracked AI prompts against the plan's cap.
- **`cycle.resetsAt`**: when the allowances reset (the billing renewal date).

Check it before a metered call when the user is near a limit, and say what's left rather than letting a call fail.
