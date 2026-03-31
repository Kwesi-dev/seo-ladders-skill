# SEO Ladders — AI Agent Skill

SEO Ladders is a full SEO content automation platform accessible via MCP. It runs the same pipeline an SEO team would — audit your site's technical health, check what keywords any domain ranks for on Google, discover site structure, then select keywords your domain can rank for based on your domain rating. From there, generate full SEO articles (3,000+ words with citations, images, FAQ, and JSON-LD schemas), publish directly to WordPress or any CMS via webhook, or save locally as Markdown + HTML. Track your keyword rankings and monitor competitors over time — all from your terminal or IDE.

## Install

```bash
npx skills add YOUR_USERNAME/seo-ladders-skill/seo-ladders
```

## Setup

1. Sign up at [seoladders.com](https://seoladders.com)
2. Go to **Dashboard > MCP Server** and create an API key
3. Add to your `.mcp.json`:

```json
{
  "mcpServers": {
    "seo-ladders": {
      "type": "http",
      "url": "https://www.seoladders.com/api/mcp",
      "headers": {
        "Authorization": "Bearer sk_live_..."
      }
    }
  }
}
```

**Other clients:** Works with Cursor, Windsurf, Codex, and any MCP-compatible tool. See [full setup guide](https://seoladders.com/features/mcp).

## The Full Pipeline

### Analyze & Discover
- **Site Audit** — Health score, broken links, missing meta tags, duplicate content, Core Web Vitals via Lighthouse
- **Check Rankings** — See what keywords any domain (yours or competitors) ranks for on Google
- **Discover Site** — Map any website's structure via sitemaps and page classification

### Research & Plan
- **Select Keywords** — Select keywords your domain can rank for based on your domain rating
- **Research Keywords** — Manual keyword search with volume, KD, intent, and DR-based recommendations
- **Content Calendar** — View, add, update, delete, and swap scheduled keywords
- **Detect Refresh Candidates** — Find published articles with ranking drops that need updating

### Generate & Publish
- **Generate Articles** — 13-step AI pipeline producing 3,000+ word articles with images, FAQ, citations, JSON-LD schemas, and internal links
- **Publish to CMS** — Push to WordPress or any webhook-based CMS (Webflow, Ghost, Strapi, etc.)
- **Save Locally** — Articles returned as Markdown + complete HTML document (meta tags, Open Graph, Twitter cards, JSON-LD, table of contents)
- **View on Dashboard** — Every generated article includes a link to view and edit on seoladders.com

## 11 Tools

| Tool | What It Does |
|------|-------------|
| `site-audit` | Full technical SEO audit with health score and Core Web Vitals |
| `check-rankings` | Check what keywords any domain ranks for on Google |
| `discover-site` | Map website structure via robots.txt and sitemaps |
| `manual-keyword-search` | Research keywords with DR-based difficulty recommendations |
| `select-monthly-keywords` | Select keywords your domain can rank for based on your DR |
| `replace-calendar-keyword` | Swap a scheduled keyword for a better alternative |
| `generate-article` | 13-step AI article generation pipeline |
| `publish-to-cms` | Publish to WordPress or any webhook CMS |
| `manage-calendar` | Content calendar CRUD operations |
| `detect-refresh-candidates` | Find articles that need content refreshes |
| `get-job-status` | Poll progress of long-running jobs |

## How to Use

Just ask naturally:

```
"Audit my website"
"What keywords does competitor.com rank for?"
"Find keywords I can win"
"Run my monthly keyword selection"
"Generate an article about best project management tools"
"Publish my latest article to WordPress"
"What content needs refreshing?"
"Show my content calendar"
```

## What You Get Back

**Articles include:**
- Full Markdown content
- Complete HTML document with meta tags, OG tags, Twitter cards, canonical URL, JSON-LD schemas, and styled table of contents
- Structured data (sections, FAQ, media plan) for programmatic use
- Direct link to view on the SEO Ladders dashboard

**Site audits include:**
- Health score (0-100) with severity label
- SSL, sitemap, robots.txt checks
- Broken links, duplicate titles/descriptions, missing H1s
- Core Web Vitals (LCP, CLS, TBT) via Lighthouse
- Actionable recommendations with affected pages

## Works With

Claude Code · Cursor · Windsurf · Codex · n8n · Any MCP Client

## Learn More

- [MCP Feature Page](https://seoladders.com/features/mcp)
- [SEO Ladders](https://seoladders.com)
