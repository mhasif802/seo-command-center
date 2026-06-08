# Content Brief

Generate a complete, ready-to-write content brief for a target keyword. The brief includes search intent, keyword cluster, SERP structure analysis, competitor word counts, and a full article outline.

Usage: `/content-brief [keyword]`
Example: `/content-brief cara apply loan kereta perodua`

## Steps

### 1. Parse the keyword
Extract the target keyword from `$ARGUMENTS`. If no keyword is provided, ask the user for one.

### 2. Keyword research
Call `mcp__dataforseo__dataforseo_labs_google_keyword_overview` for the target keyword:
- location_code: 2458 (Malaysia)
- language_code: en (or ms if keyword is in Malay)

Extract: monthly search volume, keyword difficulty, CPC, competition level, search intent.

Call `mcp__dataforseo__dataforseo_labs_google_related_keywords` with the target keyword (location: MY, limit: 30) to find semantically related terms.

Call `mcp__dataforseo__dataforseo_labs_google_keyword_suggestions` with the target keyword (location: MY, limit: 30) for long-tail variations.

Call `mcp__dataforseo__dataforseo_labs_search_intent` for the top 10 most relevant related keywords to classify intent (informational / commercial / transactional / navigational).

### 3. SERP analysis
Call `mcp__dataforseo__serp_organic_live_advanced` for the target keyword:
- location_code: 2458 (Malaysia)
- language_code: en or ms
- depth: 10

For each of the top 10 results, extract:
- Domain and URL
- Title tag
- Meta description
- Is it a blog post, landing page, or product page?
- Any SERP features present (featured snippet, PAA, local pack, video)

Call `mcp__dataforseo__on_page_content_parsing` on the top 3 competitor URLs to estimate their:
- Word count
- H2/H3 heading structure
- Whether they include images, tables, FAQs, calculators

### 4. Check your own coverage
Call `mcp__google-search-console__get_search_analytics` for `sc-domain:peroduahana.com` with the target keyword as a filter (or search for it in query results). Check if peroduahana.com already ranks for this keyword, and at what position.

### 5. Output the content brief

Format the output as:

```
# Content Brief: [Target Keyword]
Generated: [date]

---

## Keyword Intelligence

| Field | Value |
|---|---|
| Target keyword | [keyword] |
| Monthly volume (Malaysia) | [N] searches |
| Keyword difficulty | [N] / 100 |
| Search intent | [Informational / Commercial / Transactional] |
| CPC | RM [X] |
| Your current ranking | [Position N] or "Not ranking" |

## Supporting Keywords to Include
[Table: Keyword | Volume | Intent | Where to use (title / H2 / body / FAQ)]
Include the top 8–10 semantically related terms that should appear naturally in the article.

---

## SERP Landscape

| Rank | Domain | Title | Content Type | Est. Word Count |
|---|---|---|---|---|

**SERP Features present:** [Featured snippet? PAA? Video carousel? Local pack?]

**Key observation:** [1–2 sentences on what the top results have in common — are they all guides? Do they all include prices? Are they all in Malay?]

---

## Competitor Content Analysis (Top 3)

### #1: [domain]
- URL: [url]
- Title: [title]
- Heading structure: [H2s listed]
- Word count: ~[N] words
- Notable elements: [tables / calculator / FAQ / schema]

### #2: [domain]
[same format]

### #3: [domain]
[same format]

---

## Article Brief

**Recommended title** (≤60 chars, includes keyword + year + brand):
> [title]

**Recommended URL slug:**
> /blog/[slug]/

**Meta description** (≤155 chars):
> [meta]

**Target word count:** [N] words
*(Base on top 3 competitors' average + 20% more for comprehensiveness)*

**Content type:** [Long-form guide / Comparison page / FAQ page / Landing page]

**Primary audience:** [e.g. Malaysian first-time car buyers considering Perodua financing]

---

## Article Outline

### Introduction (~150 words)
- Hook: [specific angle — a question, a stat, a common pain point]
- What the article covers
- Internal link opportunity: [which existing peroduahana.com page to link to early]

### [H2 Section 1] (~200–300 words)
- Key points to cover: [bullet list]
- Supporting keyword to use: [keyword]

### [H2 Section 2] (~200–300 words)
[continue for all major sections]

[Generate 5–8 H2 sections based on competitor structure + gaps they miss]

### FAQ Section
[Generate 5 FAQ questions based on "People Also Ask" from the SERP and related keywords]
Q: [question]
A: [brief answer — 2–3 sentences that directly answer it]

### Conclusion + CTA (~100 words)
- Summarise key takeaways
- CTA: direct reader to [relevant model page or contact form on peroduahana.com]

---

### Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── BRIEF DATA START (set by /content-brief) ── */
window.BRIEF_DATA = null;
/* ── BRIEF DATA END ── */
```

Replace `window.BRIEF_DATA = null;` with:
```javascript
window.BRIEF_DATA = {
  generatedAt: "[ISO datetime]",
  keyword: "[target keyword]",
  volume: N, kd: N,
  intent: "informational",
  currentRank: N,
  recommendedTitle: "...",
  recommendedMeta: "...",
  targetWordCount: N,
  contentType: "Long-form Guide",
  supportingKeywords: [
    { keyword: "...", volume: N, intent: "...", usage: "H2 heading" },
  ],
  outline: [
    { level: "H2", text: "Introduction", keyword: null },
    { level: "H2", text: "Step-by-step guide", keyword: "cara apply loan" },
  ],
  competitors: [
    { domain: "wapcar.my", url: "https://...", title: "...", wordCount: 1800, h2s: ["H2 one","H2 two"] },
  ],
  internalLinks: [{ page: "/model/axia/", anchor: "Perodua Axia" }],
};
```
Print: `Dashboard updated → output/dashboard.html (Content Brief tab)`

## On-Page SEO Checklist
- [ ] Target keyword in H1 (exact or close match)
- [ ] Target keyword in first 100 words
- [ ] At least 3 supporting keywords used naturally in H2s
- [ ] Meta description includes target keyword
- [ ] At least 2 internal links to existing peroduahana.com pages
- [ ] FAQ schema markup (JSON-LD) for the FAQ section
- [ ] Image alt text includes keyword variant
- [ ] Article published under /blog/ with clean slug

---

## Internal Linking Suggestions
[List 3–5 existing peroduahana.com pages to link to/from this article, with suggested anchor text]
```
