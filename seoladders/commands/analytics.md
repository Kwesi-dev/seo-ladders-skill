# /analytics [question]

**Your own Google Analytics (GA4) data, for any analysis you want.** Traffic, channels, landing pages, conversions, countries, devices, visits from AI assistants, any GA4 metric and dimension over any date range. It comes live from your connection, costs nothing against your plan, and each exact request is cached for 15 minutes.

## Ready-made reports

```bash
H="Authorization: Bearer $SEO_LADDERS_API_KEY"; B=https://www.seoladders.com/api/v1/analytics

curl -s -H "$H" "$B?report=summary&days=28" | jq .        # sessions, organic, AI visits, key events, daily
curl -s -H "$H" "$B?report=ai-traffic&days=90" | jq '{totals, byAssistant, byLandingPage: .byLandingPage[:10]}'
curl -s -H "$H" "$B?report=overview&days=90" | jq .       # daily traffic
```

## Any report (custom)

Pass `metrics` and it runs a custom GA4 report:

| Param | Values |
|---|---|
| `metrics` | 1-10 GA4 API names, comma list: `sessions`, `totalUsers`, `newUsers`, `keyEvents`, `engagementRate`, `screenPageViews`... |
| `dimensions` | 0-9: `date`, `sessionSource`, `sessionMedium`, `sessionDefaultChannelGroup`, `landingPage`, `pagePath`, `country`, `deviceCategory`... |
| `startDate`, `endDate` | `YYYY-MM-DD`, `today`, `yesterday`, `30daysAgo`; or `days` (default 28) |
| `dimensionFilter` | JSON `{dimension, match, value}`; match `exact`, `contains`, `begins_with`, `ends_with`, `regex`, `full_regex`, `in_list` (with `values`); `not: true` to invert |
| `metricFilter` | JSON `{metric, op, value}`; op `eq`, `gt`, `gte`, `lt`, `lte` |
| `orderBy` | a field, `-` for descending: `-sessions` |
| `limit`, `offset` | up to 10000 rows; page with `nextOffset` |

```bash
# Organic landing pages by sessions and key events, last 90 days
curl -s -G -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/analytics \
  --data-urlencode 'metrics=sessions,keyEvents' --data-urlencode 'dimensions=landingPage' \
  --data-urlencode 'days=90' --data-urlencode 'orderBy=-sessions' \
  --data-urlencode 'dimensionFilter={"dimension":"sessionDefaultChannelGroup","match":"exact","value":"Organic Search"}' \
  | jq '.rows[:15]'
```

(MCP: `analytics` with the same fields.) Rows are flat: `{landingPage, sessions, keyEvents}`; `date` comes back as `YYYY-MM-DD`. `metadata.sampled` or `subjectToThresholding` true means GA approximated the numbers; say so.

## Not connected

`400 ga_not_connected` or `ga_property_not_selected` comes with `connectUrl` (the Traffic page, where Analytics is connected and a property picked) and `instructions_for_agent`: tell the user and don't retry. `429 ga_quota_exceeded` means wait about 15 minutes. `/seoladders-setup` shows whether Analytics is connected.

## What to do with the result

- Pair traffic with `/search-console`: GSC says what people searched, GA says what they did after they landed.
- AI-assistant visits are one part of the AI Visibility Score (`/ai-visibility`).
