# Keyword Gaps

Identify high-value keywords that peroduahana.com is NOT yet ranking for, but which are directly relevant to the site's content (Perodua cars, Malaysian automotive market). Output a prioritised content calendar sorted by the easiest wins first.

## Steps

### 1. Get your current keyword footprint
Call `mcp__google-search-console__get_search_analytics` for `sc-domain:peroduahana.com`:
- dimensions=["query"]
- dateRange: last 90 days
- rowLimit=100

This gives us the full list of queries you currently appear for. Store these as **"owned keywords"**.

### 2. Get competitor keyword data
Call `mcp__dataforseo__dataforseo_labs_google_competitors_domain` for `peroduahana.com`:
- location_code: 2458 (Malaysia)
- language_code: en

This returns the top domains that compete with peroduahana.com in SERPs. Take the **top 3 competitors** returned.

For each of the top 3 competitors, call `mcp__dataforseo__dataforseo_labs_google_ranked_keywords` to get up to 100 of their ranking keywords (location: MY, language: en).

### 3. Expand via keyword ideas
Call `mcp__dataforseo__dataforseo_labs_google_keyword_ideas` with seed keywords:
- "perodua" 
- "harga kereta perodua 2026"
- "perodua myvi"
- "cara apply loan kereta"
- "perodua alza 2026"

Use location_code: 2458 (Malaysia), language_code: en. Limit: 100 results.

Also call `mcp__dataforseo__dataforseo_labs_google_related_keywords` with seed "perodua" for additional lateral keyword discovery.

### 4. Identify gaps
Cross-reference the expanded keyword universe against your **owned keywords** list from step 1.

A keyword is a **gap** if:
- peroduahana.com does NOT rank for it (not in owned keywords), OR ranks at position 21+
- It has monthly search volume > 100
- It is topically relevant (Perodua models, car buying in Malaysia, loan/financing, insurance, comparisons)

For each gap keyword, fetch keyword difficulty via `mcp__dataforseo__dataforseo_labs_bulk_keyword_difficulty` (batch all gaps together).

### 5. Score and prioritise gaps
Calculate a **Win Score** for each gap keyword:
```
Win Score = (Monthly Volume / 100) × (100 - Keyword Difficulty)
```
Higher score = higher volume + lower competition = easiest win.

Group gaps into content clusters:
- **Model pages** (myvi, axia, bezza, alza, aruz, traz, ativa) — queries about specific models
- **Buying guides** (loan, financing, insurance, cara beli, panduan)  
- **Comparison content** (vs competitors: honda, proton, toyota)
- **Price queries** (harga, OTR, monthly instalment)
- **News/updates** (2026 model updates, facelift, new launch)

### 6. Output the content calendar

Format the output as:

```
# Keyword Gap Report — peroduahana.com
Generated: [date]

## Your Current Keyword Footprint
- Total queries appearing in GSC (90 days): [N]
- Queries with at least 1 click: [N]
- Queries ranking top 10: [N]

## Top Competitors Analysed
1. [domain] — [N] ranking keywords
2. [domain] — [N] ranking keywords  
3. [domain] — [N] ranking keywords

## Gap Summary
- Total gap keywords identified: [N]
- High-opportunity gaps (Win Score > 500): [N]
- Estimated monthly search volume being missed: [N]

---

## Content Calendar — Prioritised by Win Score

### Cluster 1: [Cluster Name]
| Keyword | Volume | KD | Win Score | Suggested Article Title | Target URL |
|---|---|---|---|---|---|
| ... | ... | ... | ... | ... | ... |

### Cluster 2: [Cluster Name]
[same table format]

[Continue for all clusters with gaps]

---

## Top 10 Articles to Write (Ordered by ROI)

1. **[Article Title]**
   - Primary keyword: [keyword] (Volume: [N], KD: [N])
   - Supporting keywords: [keyword1], [keyword2], [keyword3]
   - Estimated monthly traffic potential: [N] visits
   - Content type: [Guide / Comparison / Landing Page / FAQ]
   - Suggested URL: /blog/[slug]/ or /model/[model]/

[repeat for 2–10]

---

## Quick Wins (Existing Pages to Optimise)
[Any gaps where you already have a relevant page but aren't ranking well — these are faster than writing new content]

| Gap Keyword | Volume | KD | Existing Page to Optimise | What to Add |
|---|---|---|---|---|
```

Focus on Bahasa Melayu keywords as well as English — Malay-language content is significantly less competitive.

### After outputting the report — update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── GAPS DATA START (set by /keyword-gaps) ── */
window.GAPS_DATA = null;
/* ── GAPS DATA END ── */
```

Replace `window.GAPS_DATA = null;` with:
```javascript
window.GAPS_DATA = {
  generatedAt: "[ISO datetime]",
  ownedKeywords: N,              // total queries in GSC
  competitorsAnalyzed: ["wapcar.my", "..."],
  totalMissedVolume: N,          // sum of gap keyword volumes
  clusters: [
    {
      name: "Model Pages",       // cluster name
      keywords: [
        {
          keyword: "perodua myvi 2026",
          volume: 8100, kd: 38,
          winScore: 498,         // (volume/100) × (100-kd)
          suggestedTitle: "...",
          targetUrl: "/model/myvi/",
          contentType: "Landing Page",
        },
      ],
    },
    // ... more clusters
  ],
  topArticles: [
    { title: "Cara Apply Loan Kereta...", keyword: "...", volume: N, kd: N, winScore: N, contentType: "Guide", url: "/blog/slug/" },
    // ... top 10
  ],
};
```
Print: `Dashboard updated → output/dashboard.html`
