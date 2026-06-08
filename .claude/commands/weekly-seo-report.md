# Weekly SEO Report

Generate a comprehensive weekly SEO performance report for peroduahana.com by pulling live data from all three MCPs, then synthesising it into an actionable Markdown report.

## Steps

### 1. Establish the date range
- Today's date is available in context. Use the last 7 days as the reporting window (today minus 7 days → today).

### 2. Pull Google Search Console data
Use `mcp__google-search-console__get_search_analytics` with these calls:
- **Top queries**: dimensions=["query"], rowLimit=20, sorted by clicks desc then impressions desc. Site: `sc-domain:peroduahana.com`
- **Top pages**: dimensions=["page"], rowLimit=15, sorted by clicks desc
- **Query × Page**: dimensions=["query","page"], rowLimit=10 — to link which page serves which query

Also call `mcp__google-search-console__get_performance_overview` for the period total (clicks, impressions, avg CTR, avg position).

Also call `mcp__google-search-console__check_indexing_issues` to surface any coverage errors.

### 3. Pull Google Analytics data
Use `mcp__google-analytics__run_report` with:
- **Sessions by landing page**: metrics=["sessions","bounceRate","averageSessionDuration"], dimensions=["landingPage"], dateRanges=[last 7 days], orderBys=sessions desc, limit=15
- **Traffic by channel**: metrics=["sessions"], dimensions=["sessionDefaultChannelGroup"], dateRanges=[last 7 days]
- **New vs returning users**: metrics=["newUsers","activeUsers"], dimensions=["newVsReturning"], dateRanges=[last 7 days]

### 4. Pull DataForSEO keyword context
For the **top 5 queries by impressions** from step 2, call `mcp__dataforseo__dataforseo_labs_google_keyword_overview` (location: Malaysia, language: en/ms) to enrich each with:
- Monthly search volume
- Keyword difficulty
- Search intent

Also call `mcp__dataforseo__dataforseo_labs_google_domain_rank_overview` for `peroduahana.com` to get the domain's overall authority score and estimated organic traffic.

### 5. Format the report

Output a Markdown report with this structure. Save it as `peroduahana.report.YYYY-MM-DD.md` in the working directory using the Write tool.

```
# SEO Performance Snapshot — peroduahana.com
**Period: [start] – [end]**
**Generated: [today]**

---

## 1. Performance Overview (GSC)
[Total clicks, impressions, avg CTR, avg position — compare to prior week if data available]

## 2. Top Queries
[Table: Query | Impressions | Clicks | CTR | Avg Position | KD | Volume]
[Flag queries at position 4–15 as "Near page 1 — optimize now"]
[Flag queries at 0 clicks as "Zero CTR alert"]

## 3. Top Landing Pages
[Table: Page | Sessions | Clicks (GSC) | CTR | Bounce Rate]

## 4. Traffic Channels (GA4)
[Table: Channel | Sessions | % of Total]

## 5. Indexing & Technical Issues
[Any errors from check_indexing_issues]

## 6. Domain Authority
[DataForSEO domain rank overview: Authority Score, Organic Traffic est., Referring Domains]

## Priority Actions This Week
[3–5 numbered, specific, highest-leverage actions derived from the data]
[Each action must reference specific pages, queries, or metrics from the report]

## Summary Scorecard
[Table: Area | Status | Urgency]
```

### 6. Update the HTML dashboard

After saving the Markdown report, update `output/dashboard.html` so the dashboard reflects the new data.

Use the **Edit tool** to replace exactly the block between:
```
/* ── OVERVIEW DATA START (set by /weekly-seo-report) ── */
window.OVERVIEW_DATA = { ... };
/* ── OVERVIEW DATA END ── */
```

Replace `window.OVERVIEW_DATA = { ... };` with a freshly constructed object. Schema:

```javascript
window.REPORT_DATA = {
  site: "peroduahana.com",
  period: { start: "[human date e.g. Jun 2, 2026]", end: "[human date]" },
  generatedAt: "[ISO datetime string]",
  kpi: {
    clicks:      { value: [total clicks this week], prev: [total clicks prior week or 0] },
    impressions: { value: [total impressions],       prev: [prior week or 0] },
    ctr:         { value: [avg CTR as decimal],      prev: [prior week or 0] },
    avgPosition: { value: [avg position],            prev: [prior week or 0] },
  },
  timeseries: {
    labels:      ["Mon", "Tue", ...],   // 7 day labels
    clicks:      [0, 0, ...],           // clicks per day
    impressions: [1, 2, ...],           // impressions per day
  },
  channels: [
    { name: "Organic Search", sessions: N, color: "#10b981" },
    { name: "Direct",         sessions: N, color: "#6366f1" },
    { name: "Referral",       sessions: N, color: "#06b6d4" },
    // add any other channels from GA4, assign a hex color
  ],
  queries: [
    // top 10 queries from GSC, enriched with KD + volume from DataForSEO
    {
      query: "...", impressions: N, clicks: N, ctr: 0.0,
      position: N.N, kd: N, volume: N,
      // status rules:
      // "near_page_1" if position 11–20
      // "ranking"     if position ≤10 AND clicks > 0
      // "zero_ctr"    if position ≤10 AND clicks === 0
      // "deep"        if position > 20
      status: "deep",
    },
  ],
  pages: [
    // top landing pages from GA4, joined with GSC click data where available
    { page: "/", sessions: N, clicks: N, bounceRate: N },
  ],
  domain: {
    authorityScore:   N,  // from DataForSEO domain_rank_overview
    referringDomains: N,
    totalBacklinks:   N,
    estTraffic:       N,
  },
  actions: [
    // 4–6 priority actions derived from the data, ordered: critical → high → medium
    {
      urgency: "critical",   // "critical" | "high" | "medium"
      title: "...",
      description: "...",    // 1–2 sentences, specific and actionable
    },
  ],
};
```

If a DataForSEO call fails or returns no data for a field, use `0` for numbers and `"N/A"` for strings — never leave the object malformed.

### 7. Print a concise summary to the terminal
After saving both files, print the "Priority Actions" section to the terminal so the user sees the key takeaways immediately without opening any file. Also print:
`Dashboard updated → output/dashboard.html — open in browser to view.`
