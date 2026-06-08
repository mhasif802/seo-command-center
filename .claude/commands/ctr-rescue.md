# CTR Rescue

Find pages and queries on peroduahana.com that are ranking on page 1–2 (positions 4–20) but earning zero or very low clicks. For each one, analyse the SERP competition and produce rewritten title tags and meta descriptions to rescue the CTR.

## Steps

### 1. Pull GSC opportunity data
Call `mcp__google-search-console__get_search_analytics` for `sc-domain:peroduahana.com`:
- dimensions=["query","page"]
- dateRange: last 28 days (longer window gives more stable position data)
- rowLimit=50
- Filter: position <= 20

From the results, calculate an **Opportunity Score** for each query:
```
Opportunity Score = Impressions × (expected_CTR_at_position - actual_CTR)
```
Where expected_CTR_at_position is:
- Pos 1: 28%, Pos 2: 15%, Pos 3: 10%, Pos 4: 7%, Pos 5: 5%
- Pos 6–10: 3%, Pos 11–15: 1.5%, Pos 16–20: 0.8%

Sort by Opportunity Score descending. Take the **top 8 candidates**.

### 2. For each candidate — fetch SERP data
Call `mcp__dataforseo__serp_organic_live_advanced` for each of the top 8 queries:
- location_code: 2458 (Malaysia)
- language_code: ms or en (match query language)
- depth: 10 (top 10 results only)

Extract from the results:
- Title tag of the result ranking above yours
- Meta description of the result ranking above yours
- Your current result's title and description (identify by peroduahana.com domain)
- Featured snippet present? (yes/no)
- People Also Ask present? (yes/no)

### 3. Analyse each candidate
For each of the 8 queries, produce a structured analysis block:

```
### [Query]
**Your ranking**: Position [X] on [page URL]
**Impressions (28d)**: [N] | **Clicks**: [N] | **Your CTR**: [X%] | **Expected CTR**: [Y%]
**Opportunity Score**: [Z]

**Current title**: [your current title tag — fetch from DataForSEO SERP result]
**Current meta**: [your current meta description]

**What's beating you** (Position 1 competitor):
- Title: [competitor title]
- Meta: [competitor meta]

**Why they're winning**: [1–2 sentence analysis: year in title? price mentioned? CTA? Malay language? schema rich snippet?]

**Rewritten title** (≤60 chars):
> [new title — include year 2026, price hook or key differentiator, brand "Perodua Hana"]

**Rewritten meta** (≤155 chars):
> [new meta — include primary keyword naturally, specific benefit, call to action]

**Additional quick wins**:
- [Any schema opportunity, FAQ, featured snippet structure suggestion]
```

### 4. Priority summary table
After all 8 analyses, output a summary table:

| Query | Position | Impressions | Opp. Score | Action |
|---|---|---|---|---|
| ... | ... | ... | ... | Rewrite title + meta |

### 5. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── CTR DATA START (set by /ctr-rescue) ── */
window.CTR_DATA = null;
/* ── CTR DATA END ── */
```

Replace `window.CTR_DATA = null;` with:
```javascript
window.CTR_DATA = {
  generatedAt: "[ISO datetime]",
  period: "last 28 days",
  opportunities: [
    {
      query: "...",
      page: "/model/alza/",
      position: 9.0,
      impressions: 45,
      clicks: 0,
      actualCtr: 0.00,
      expectedCtr: 0.07,
      opportunityScore: 315,    // impressions × (expectedCtr - actualCtr)
      currentTitle: "...",      // as seen in SERP
      currentMeta: "...",
      rewrittenTitle: "...",    // your rewritten version (≤60 chars)
      rewrittenMeta: "...",     // your rewritten version (≤155 chars)
      serpFeatures: ["PAA"],    // SERP features present
    },
    // ... up to 8 opportunities
  ],
};
```

### 6. Implementation instructions
End the terminal output with:
```
## How to Implement
1. Log into your CMS/WordPress
2. For each page above, update the SEO title and meta description
3. After updating, request re-crawl: Search Console → URL Inspection → Request Indexing
4. Re-run /ctr-rescue in 2 weeks to measure improvement
Dashboard updated → output/dashboard.html
```

Focus especially on Malay-language queries (write Malay titles back in Malay), model-specific queries (include model name + year), and price queries (include "dari RM[X]" if you can infer the price).
