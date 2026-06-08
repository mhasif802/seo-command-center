# Competitor Spy

Deep-dive into a competitor's SEO profile — every keyword they rank for that peroduahana.com doesn't, their top pages, backlink gap, and the 20 easiest keywords to steal from them.

Usage: `/competitor-spy [domain]`
Example: `/competitor-spy wapcar.my`

## Steps

### 1. Parse the target domain
Extract the competitor domain from `$ARGUMENTS`. If none provided, use the top competitor returned by DataForSEO for peroduahana.com (run `mcp__dataforseo__dataforseo_labs_google_competitors_domain` first and take #1).

### 2. Competitor keyword profile
Call `mcp__dataforseo__dataforseo_labs_google_ranked_keywords` for the competitor domain:
- location_code: 2458 (Malaysia)
- language_code: en
- limit: 200
- order_by: traffic_cost desc (highest-value traffic first)

Call `mcp__dataforseo__dataforseo_labs_google_domain_rank_overview` for the competitor domain to get:
- Authority score
- Organic keywords count
- Estimated organic traffic
- Organic traffic cost (traffic value)

### 3. Your own keyword profile
Call `mcp__google-search-console__get_search_analytics` for `sc-domain:peroduahana.com`:
- dimensions=["query"]
- dateRange: last 90 days
- rowLimit: 200

This is your **owned keyword set**.

### 4. Find the keyword gap
Call `mcp__dataforseo__dataforseo_labs_google_domain_intersection` with:
- targets: [competitor_domain, "peroduahana.com"]
- location_code: 2458

This shows keywords BOTH rank for — useful for seeing where you overlap.

The **gap** = competitor's ranked keywords MINUS your owned keywords (from GSC) MINUS any overlap from domain_intersection.

For the gap keywords, call `mcp__dataforseo__dataforseo_labs_bulk_keyword_difficulty` in batches to get KD scores.

### 5. Score and filter gaps
For each gap keyword, calculate:
```
Win Score = (Monthly Volume / 100) × (100 - KD)
```

Filter to only keywords that are:
- Topically relevant to Perodua cars, Malaysian automotive, car buying, financing, insurance
- Volume > 50/month
- KD < 60 (achievable for a newer site)

Sort by Win Score descending. Take the top 50 for the report, highlight the top 20.

### 6. Competitor top pages analysis
Call `mcp__dataforseo__backlinks_domain_pages` for the competitor domain to identify their highest-traffic pages.

For their top 5 pages, call `mcp__dataforseo__on_page_content_parsing` to get:
- Page title and URL
- H1 and H2 structure
- Approximate content depth

### 7. Backlink gap
Call `mcp__dataforseo__backlinks_summary` for both `peroduahana.com` and the competitor domain.

Compare:
- Total backlinks
- Referring domains
- Authority score
- Dofollow vs nofollow ratio

### 8. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── COMPETITOR DATA START (set by /competitor-spy) ── */
window.COMPETITOR_DATA = null;
/* ── COMPETITOR DATA END ── */
```

Replace `window.COMPETITOR_DATA = null;` with:
```javascript
window.COMPETITOR_DATA = {
  generatedAt: "[ISO datetime]",
  competitor: "[domain]",
  comparison: {
    you:  { authScore: N, keywords: N, traffic: N, domains: N },
    them: { authScore: N, keywords: N, traffic: N, domains: N },
  },
  topGaps: [
    {
      keyword: "perodua myvi review",
      theirRank: 3, volume: 4400, kd: 35, winScore: 286,
      contentType: "Blog Post",
    },
    // top 20 gaps
  ],
  topPages: [
    {
      url: "https://wapcar.my/...",
      keyword: "perodua myvi 2026",
      traffic: 12000,
      title: "Perodua Myvi 2026 — Full Review",
      howToBeat: "Write a longer guide with current 2026 OTR pricing and video embed.",
    },
    // top 5 pages
  ],
};
```
Print: `Dashboard updated → output/dashboard.html (Competitor tab)`

### 9. Output the Markdown competitor spy report

```
# Competitor Spy Report: [competitor domain] vs peroduahana.com
Generated: [date]

---

## Competitor Overview

| Metric | [Competitor] | peroduahana.com |
|---|---|---|
| Authority Score | [N] | [N] |
| Organic Keywords | [N] | [N] |
| Est. Monthly Traffic | [N] | [N] |
| Traffic Value (USD) | $[N] | $[N] |
| Referring Domains | [N] | [N] |
| Total Backlinks | [N] | [N] |

---

## Keyword Gap — Top 20 Easiest Wins

These are keywords [competitor] ranks for that peroduahana.com does NOT rank for, sorted by easiest to capture:

| # | Keyword | [Competitor] Rank | Volume/mo | KD | Win Score | Content Type Needed |
|---|---|---|---|---|---|---|
| 1 | [keyword] | [pos] | [N] | [N] | [score] | [blog post / model page / comparison] |
...

---

## Full Gap List (Top 50)

[Same table format, all 50 keywords]

---

## Competitor's Top 5 Pages (What's Driving Their Traffic)

### 1. [URL]
- Traffic est.: [N] visits/month
- Primary keyword: [keyword]
- Title: [title]
- Content angle: [what makes this page rank well]
- **How to beat it**: [specific recommendation for peroduahana.com — what to write, what angle to take]

[repeat for pages 2–5]

---

## Backlink Gap

[Competitor] has [N] more referring domains than peroduahana.com. Key domains linking to them but not you:

| Referring Domain | Authority Score | Anchor Text | Link Type |
|---|---|---|---|

**Link building recommendation:** [1–2 actionable suggestions — e.g. automotive directories, Malay motoring blogs, Perodua community forums]

---

## 30-Day Action Plan to Capture These Keywords

1. **Week 1**: Write [top 2 gap keywords] — estimated effort [S/M/L], estimated traffic gain [N] visits/mo
2. **Week 2**: Optimise existing [model pages] for [keyword cluster]
3. **Week 3**: Build [N] backlinks via [specific tactic]
4. **Week 4**: [next priority]
```
