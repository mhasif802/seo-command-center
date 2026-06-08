# Technical SEO Audit

Run a full technical SEO audit of peroduahana.com using DataForSEO's on-page crawler and Google Search Console indexing data. Surface broken links, missing meta tags, slow pages, duplicate content, schema errors, and indexation issues.

Usage: `/technical-audit`

## Steps

### 1. Crawl the site with DataForSEO On-Page
Call `mcp__dataforseo__on_page_instant_pages` for the peroduahana.com homepage first to verify the crawler can access the site:
- url: "https://peroduahana.com"
- load_resources: true
- enable_javascript: true
- custom_js: null

Then for key pages, call `mcp__dataforseo__on_page_instant_pages` on each major URL:
- https://peroduahana.com (homepage)
- https://peroduahana.com/blog/
- https://peroduahana.com/model/myvi/
- https://peroduahana.com/model/axia/
- https://peroduahana.com/model/bezza/
- https://peroduahana.com/model/alza/
- https://peroduahana.com/model/aruz/
- https://peroduahana.com/model/traz/

For each page extract:
- HTTP status code
- Title tag (present? length? keyword?)
- Meta description (present? length?)
- H1 tag (present? how many?)
- Canonical tag (present? self-referencing?)
- Robots meta (index/noindex/nofollow)
- Page load time
- Mobile viewport meta tag
- Structured data / schema markup (present? type?)
- Internal links on the page
- External links on the page
- Images missing alt text

### 2. Run Lighthouse for Core Web Vitals
Call `mcp__dataforseo__on_page_lighthouse` for the homepage and 2–3 key model pages:
- url: "https://peroduahana.com"
- categories: ["performance", "seo", "accessibility", "best-practices"]

Extract:
- Performance score (0–100)
- LCP (Largest Contentful Paint) — target: < 2.5s
- CLS (Cumulative Layout Shift) — target: < 0.1
- FID/INP (Interaction to Next Paint) — target: < 200ms
- SEO score
- Specific audit failures

### 3. GSC indexing and coverage
Call `mcp__google-search-console__check_indexing_issues` for `sc-domain:peroduahana.com`:
- Extract: pages with errors, excluded pages, valid pages

Call `mcp__google-search-console__get_sitemaps` for the property:
- Is a sitemap submitted?
- When was it last crawled?
- Any sitemap errors?

Call `mcp__google-search-console__batch_url_inspection` on all key model page URLs to check:
- Indexing status (Indexed / Not indexed)
- Coverage state
- Last crawl date
- Canonical as Google sees it

### 4. Content quality checks
From the on-page crawl data:
- **Thin content**: pages with very low word count (< 300 words) — especially model pages
- **Duplicate titles**: any two pages sharing the same title tag
- **Missing titles**: pages with no title tag or generic titles
- **Duplicate meta descriptions**: any two pages sharing the same meta description
- **Multiple H1s**: pages with more than one H1 tag
- **Missing H1**: pages with no H1

### 5. Schema markup audit
For each crawled page, check the structured data found. Flag:
- Pages with NO schema (especially model pages — should have `Car` or `Product` schema)
- Missing `LocalBusiness` schema on homepage
- Missing `BreadcrumbList` schema on model pages
- Missing `FAQPage` schema on blog posts that have Q&A content

### 6. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── TECH DATA START (set by /technical-audit) ── */
window.TECH_DATA = null;
/* ── TECH DATA END ── */
```

Replace `window.TECH_DATA = null;` with:
```javascript
window.TECH_DATA = {
  generatedAt: "[ISO datetime]",
  lighthouse: {
    performance: N,      // 0–100
    seo: N,
    accessibility: N,
    bestPractices: N,
    lcp: "2.8s",         // string with unit
    cls: "0.12",         // string
    inp: "180ms",        // string with unit
  },
  pages: [
    {
      url: "/",
      status: 200,             // HTTP status code
      title: "Page Title",
      titleLen: 45,
      titleIssue: null,        // null = OK, or string describing the issue
      meta: "Meta description text",
      metaLen: 120,
      metaIssue: null,
      h1Count: 1,
      h1Issue: null,
      canonical: "/",
      indexed: true,
      loadTime: 1.2,           // seconds
      schema: ["LocalBusiness"],  // schema types found, [] if none
    },
    // ... all crawled pages
  ],
  issues: {
    critical: [{ page: "/model/alza/", issue: "Missing H1 tag" }],
    high:     [{ page: "/model/myvi/", issue: "Title tag too long (78 chars)" }],
    medium:   [{ page: "/blog/",       issue: "No schema markup" }],
  },
};
```
Print: `Dashboard updated → output/dashboard.html`

### 7. Output the Markdown audit report

```
# Technical SEO Audit — peroduahana.com
Generated: [date]

---

## Executive Summary

| Category | Score | Critical Issues |
|---|---|---|
| Indexation | [Good/Warning/Critical] | [N] issues |
| On-Page Fundamentals | [Good/Warning/Critical] | [N] issues |
| Core Web Vitals | [Good/Warning/Critical] | [N] issues |
| Schema / Structured Data | [Good/Warning/Critical] | [N] issues |
| Content Quality | [Good/Warning/Critical] | [N] issues |

---

## 1. Indexation Status

| Page | Status | Last Crawled | Canonical | Issue |
|---|---|---|---|---|
| / (Homepage) | [Indexed / Not indexed] | [date] | [canonical URL] | [issue or OK] |
| /model/myvi/ | [status] | [date] | [canonical] | [issue] |
[... all pages]

**Sitemap:** [Submitted / Not submitted] | Last crawled: [date] | Errors: [N]

---

## 2. On-Page Fundamentals

### Title Tags
| Page | Title | Length | Issue |
|---|---|---|---|
| / | [title] | [N chars] | [Missing / Too long / Duplicate / OK] |

### Meta Descriptions
| Page | Meta Description | Length | Issue |
|---|---|---|---|

### H1 Tags
| Page | H1 | Count | Issue |
|---|---|---|---|

---

## 3. Core Web Vitals (Lighthouse)

### Homepage (https://peroduahana.com)
| Metric | Score | Value | Target | Status |
|---|---|---|---|---|
| Performance | [0–100] | — | 90+ | [Pass/Fail] |
| LCP | — | [Xs] | < 2.5s | [Pass/Fail] |
| CLS | — | [0.X] | < 0.1 | [Pass/Fail] |
| INP | — | [Xms] | < 200ms | [Pass/Fail] |
| SEO Score | [0–100] | — | 90+ | [Pass/Fail] |

**Top Lighthouse failures:**
[List specific failed audits with their impact level]

---

## 4. Schema / Structured Data

| Page | Schema Found | Schema Missing | Priority |
|---|---|---|---|
| / | LocalBusiness | — | — |
| /model/myvi/ | — | Car, Product | **HIGH** |
| /blog/[post] | — | Article, FAQPage | Medium |

**Schema implementation templates** (copy these into your CMS):

```json
<!-- Car schema — add to each model page -->
{
  "@context": "https://schema.org",
  "@type": "Car",
  "name": "[Model Name] [Year]",
  "brand": { "@type": "Brand", "name": "Perodua" },
  "offers": {
    "@type": "Offer",
    "price": "[OTR Price]",
    "priceCurrency": "MYR",
    "availability": "https://schema.org/InStock"
  }
}
```

---

## 5. Content Quality Issues

| Issue | Pages Affected | Fix |
|---|---|---|
| Thin content (< 300 words) | [N pages] | Add pricing table, specs, FAQ, comparison |
| Duplicate title tags | [N pairs] | Rewrite titles to be unique |
| Missing meta descriptions | [N pages] | Write unique 140–155 char meta for each |
| Images without alt text | [N images] | Add descriptive alt text with keyword |
| Multiple H1 tags | [N pages] | Keep only one H1 per page |

---

## Priority Fix List

### Fix immediately (Critical)
[Issues that are blocking indexation or causing ranking losses]

### Fix this week (High)
[Issues that are suppressing CTR or rankings]

### Fix this month (Medium)
[Performance, schema, content improvements]

---

## Tracking Progress
Re-run `/technical-audit` after completing fixes to verify improvements. Target: all Critical issues resolved within 7 days.
```
