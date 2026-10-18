# /settings

The current automation settings. Free. Read this before changing either setting.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/settings | jq '{autofill, autoPublish, reviewMode, cms}'
```

(MCP: `settings`.)

- **`autofill`**: the agent fills the content calendar on its own. On by default.
- **`autoPublish`**: articles go live on the connected CMS when written. It switches on by default once a CMS is connected; `reviewMode: true` means each article waits for approval.
- **`cms`**: whether a CMS is connected, and which.

To change them, use the autofill and auto-publish commands in `SKILL.md` (MCP: `autoblog-autofill`, `auto-publish`), and confirm with the user first.
