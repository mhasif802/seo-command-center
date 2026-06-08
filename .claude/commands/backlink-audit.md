# Backlink Audit

Pull a full backlink profile for peroduahana.com — authority score, referring domains, new/lost links, toxic link flags, and a link-building action plan.

Usage: `/backlink-audit`

## Steps

### 1. Summary overview
Call `mcp__dataforseo__backlinks_summary` for `peroduahana.com`:
- Extract: domain authority score, total backlinks, referring domains, dofollow count, nofollow count, edu/gov links, broken links.

### 2. Full backlink list
Call `mcp__dataforseo__backlinks_backlinks` for `peroduahana.com`:
- limit: 100
- order_by: rank desc (highest-authority links first)
- Extract for each: source URL, anchor text, dofollow/nofollow, authority score of source, date first seen.

### 3. Referring domains breakdown
Call `mcp__dataforseo__backlinks_referring_domains` for `peroduahana.com`:
- limit: 50
- Extract: domain, authority score, total links from that domain, dofollow ratio.

### 4. New and lost links (last 30 days)
Call `mcp__dataforseo__backlinks_bulk_new_lost_backlinks` for `peroduahana.com`:
- date_from: 30 days ago
- Separate new links from lost links.

Call `mcp__dataforseo__backlinks_timeseries_new_lost_summary` for `peroduahana.com`:
- date_from: 90 days ago
- To show the trend of link acquisition over time.

### 5. Spam / toxic link detection
Call `mcp__dataforseo__backlinks_bulk_spam_score` for `peroduahana.com`:
- This returns a spam score for the backlink profile.

From the full backlink list (step 2), flag any links where:
- Source domain authority score < 5
- Anchor text is exact-match keyword stuffed (e.g. "buy cheap car malaysia")
- Source domain is not in English or Malay (possible foreign spam)
- Source appears to be a link farm (many outbound links, low content)

### 6. Anchor text analysis
Call `mcp__dataforseo__backlinks_anchors` for `peroduahana.com`:
- Analyse the anchor text distribution.
- Flag over-optimisation: if any single exact-match anchor is > 30% of all anchors, flag it.

### 7. Competitor comparison
Call `mcp__dataforseo__backlinks_summary` for the top 2 competitors (run `mcp__dataforseo__dataforseo_labs_google_competitors_domain` for peroduahana.com to get them).
- Compare authority score, referring domains, total links side by side.

### 8. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── BACKLINK DATA START (set by /backlink-audit) ── */
window.BACKLINK_DATA = null;
/* ── BACKLINK DATA END ── */
```

Replace `window.BACKLINK_DATA = null;` with:
```javascript
window.BACKLINK_DATA = {
  generatedAt: "[ISO datetime]",
  summary: {
    authorityScore: N, totalBacklinks: N, referringDomains: N,
    dofollow: N, nofollow: N, spamScore: N,
  },
  topLinks: [
    { domain: "paultan.org", authScore: 72, anchor: "perodua hana", type: "dofollow", dateFound: "Jun 2026" },
    // top 10 by authScore
  ],
  newLinks: [
    { source: "https://...", domain: "...", authScore: N, anchor: "...", date: "..." },
    // links gained in last 30 days
  ],
  lostLinks: [
    { source: "https://...", domain: "...", authScore: N, anchor: "...", date: "..." },
    // links lost in last 30 days
  ],
  anchors: [
    { text: "perodua hana", count: N, pct: N, status: "Branded" },
    // top anchors
  ],
};
```
Print: `Dashboard updated → output/dashboard.html`

### 9. Output the Markdown audit report

```
# Backlink Audit — peroduahana.com
Generated: [date]

---

## Authority Overview

| Metric | Value |
|---|---|
| Domain Authority Score | [N] / 100 |
| Total Backlinks | [N] |
| Referring Domains | [N] |
| Dofollow Backlinks | [N] ([X]%) |
| Nofollow Backlinks | [N] ([X]%) |
| Edu/Gov Links | [N] |
| Spam Score | [N] / 100 — [Low / Medium / High risk] |

---

## vs. Top Competitors

| Domain | Authority Score | Referring Domains | Total Backlinks |
|---|---|---|---|
| peroduahana.com | [N] | [N] | [N] |
| [competitor 1] | [N] | [N] | [N] |
| [competitor 2] | [N] | [N] | [N] |

**Gap to close:** peroduahana.com needs ~[N] more referring domains to match [competitor 1].

---

## Top 20 Backlinks (Highest Authority)

| Source Domain | Auth. Score | Anchor Text | Type | Date Found |
|---|---|---|---|---|

---

## New Links (Last 30 Days)
[Table: Source | Anchor | Auth. Score | Date]

## Lost Links (Last 30 Days)
[Table: Source | Anchor | Auth. Score | Date Lost]
[Flag any high-authority lost links as "Priority to reclaim"]

---

## Anchor Text Distribution

| Anchor Text | Count | % of Total | Status |
|---|---|---|---|
| [anchor] | [N] | [X]% | [OK / Over-optimised / Branded] |

---

## Toxic / Suspicious Links

[If none: "No toxic links detected. Profile looks clean."]
[If found:]
| Source URL | Auth. Score | Anchor | Issue |
|---|---|---|---|

**Recommendation:** [Disavow file needed? Or just monitor?]

---

## Link Building Action Plan

### Quick wins (next 30 days)
1. **Reclaim lost links**: [List any high-authority lost links with outreach suggestion]
2. **Local citations**: Submit peroduahana.com to Malaysian automotive directories — [list 3–5 specific directories]
3. **Perodua community**: [Specific forums, Facebook groups, or blogs where a link is natural]

### Medium-term (60–90 days)
4. **Content-driven links**: The car loan guide (planned content) should attract links from personal finance blogs
5. **Journalist outreach**: Automotive journalists at [paultan.org, carlist.my, wapcar.my] — pitch them data or a quote
6. **Perodua official**: Check if perodua.com.my has a dealer directory — get listed

### KPI targets
- **Month 1**: Reach [N+5] referring domains (currently [N])
- **Month 3**: Reach authority score [current+5]
- **Month 6**: Match [competitor 1]'s referring domain count of [N]
```
