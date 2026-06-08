# AI Visibility

Track how peroduahana.com and the Perodua Hana brand appear in AI-generated responses (ChatGPT, Gemini, etc.). This is Generative Engine Optimisation (GEO) — measuring your presence in the AI search layer, not just Google.

Usage: `/ai-visibility` or `/ai-visibility [brand/keyword to check]`

## Steps

### 1. Parse input
Extract brand or keyword from `$ARGUMENTS`. If none provided, default to checking both:
- `peroduahana.com`
- `Perodua Hana`
- `perodua dealer`

### 2. Get available LLM models tracked
Call `mcp__dataforseo__ai_optimization_llm_models` to see which AI models DataForSEO monitors (ChatGPT, Gemini, etc.).

### 3. Fetch LLM mention data for the brand
Call `mcp__dataforseo__ai_opt_llm_ment_search` for each target brand/keyword:
- Search for mentions of "peroduahana.com" and "Perodua Hana" across AI responses
- Note sentiment (positive / neutral / negative) and context

Call `mcp__dataforseo__ai_opt_llm_ment_agg_metrics` for aggregated mention metrics:
- Total mentions
- Mention rate (what % of relevant AI queries mention you)
- Sentiment breakdown
- Which AI models mention you most

Call `mcp__dataforseo__ai_opt_llm_ment_top_domains` to see which automotive/Perodua domains are most mentioned by AI models — this shows you who AI considers authoritative in your space.

Call `mcp__dataforseo__ai_opt_llm_ment_top_pages` to find which specific pages are being cited by AI responses.

### 4. Keyword-level AI visibility
Call `mcp__dataforseo__ai_opt_kw_data_loc_and_lang` to get location/language parameters available.

Call `mcp__dataforseo__ai_optimization_keyword_data_search_volume` for key Perodua-related queries to see their AI search volume (how often these are asked to AI assistants):
- "perodua dealer malaysia"
- "harga perodua 2026"
- "cara beli kereta perodua"
- "perodua loan calculator"
- "perodua myvi review"

### 5. Check ChatGPT directly for brand mention
Call `mcp__dataforseo__ai_optimization_chat_gpt_scraper` with the prompt:
- "What are the best Perodua car dealers in Malaysia?"
- "Where can I find Perodua car prices in Malaysia?"
- "Best website for Perodua information Malaysia"

See if peroduahana.com is mentioned in the responses.

### 6. Cross-metric analysis
Call `mcp__dataforseo__ai_opt_llm_ment_cross_agg_metrics` to compare peroduahana.com against competitor domains in AI mention share.

### 7. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── AI DATA START (set by /ai-visibility) ── */
window.AI_DATA = null;
/* ── AI DATA END ── */
```

Replace `window.AI_DATA = null;` with:
```javascript
window.AI_DATA = {
  generatedAt: "[ISO datetime]",
  mentionRate: 0.02,           // decimal, e.g. 0.02 = 2%
  sentiment: "neutral",        // "positive" | "neutral" | "negative"
  topModels: [
    { model: "ChatGPT", mentions: N },
    { model: "Gemini",  mentions: N },
  ],
  chatgptResponses: [
    {
      query: "Best Perodua dealers in Malaysia?",
      response: "Full response text from ChatGPT...",
      mentioned: false,        // true if peroduahana.com or Perodua Hana is in the response
    },
    // 2–3 queries
  ],
  topDomains: [
    { domain: "wapcar.my",         mentionShare: 0.34 },
    { domain: "paultan.org",       mentionShare: 0.28 },
    { domain: "peroduahana.com",   mentionShare: 0.02 },
    // top 8 domains by mention share
  ],
  aiSearchVolume: [
    { keyword: "perodua dealer malaysia", googleVol: 1200, aiVol: 340 },
  ],
};
```
Print: `Dashboard updated → output/dashboard.html`

### 8. Output the Markdown AI visibility report

```
# AI Visibility Report — peroduahana.com
Generated: [date]

---

## What is AI Visibility?
AI assistants (ChatGPT, Gemini, Perplexity) are now answering car-buying questions directly. If your site isn't mentioned in these responses, you're invisible to a growing share of car buyers. This report measures your presence in the AI layer.

---

## Brand Mention Summary

| Brand/Domain | Total AI Mentions | Mention Rate | Avg Sentiment | Top AI Model |
|---|---|---|---|---|
| peroduahana.com | [N] | [X]% | [Positive/Neutral/Negative] | [ChatGPT/Gemini] |
| Perodua Hana | [N] | [X]% | [X] | [X] |

**Benchmark — Top domains in your space mentioned by AI:**
| Domain | AI Mention Share |
|---|---|
| [top competitor] | [X]% |
| [competitor 2] | [X]% |
| peroduahana.com | [X]% |

---

## ChatGPT Direct Responses

**Query: "What are the best Perodua car dealers in Malaysia?"**
> [Paste actual ChatGPT response]
> peroduahana.com mentioned: **Yes / No**

**Query: "Best website for Perodua information Malaysia?"**
> [Paste actual ChatGPT response]
> peroduahana.com mentioned: **Yes / No**

---

## AI Search Volume — Key Queries

| Query | Traditional Google Volume | AI Query Volume | Trend |
|---|---|---|---|
| perodua dealer malaysia | [N] | [N] | [↑/↓/→] |
| harga perodua 2026 | [N] | [N] | [↑/↓/→] |

---

## Pages Being Cited by AI (Industry-wide)

These peroduahana.com pages (if any) are being referenced by AI responses:
| Page URL | AI Citations | Context |
|---|---|---|

---

## GEO Action Plan

### Why you're not being mentioned (likely causes)
[Based on data — e.g. low domain authority, no structured data, content not comprehensive enough]

### How to get AI to mention peroduahana.com

1. **Structured data (schema)**: AI models favour pages with clear, machine-readable data
   - Add `Automobile` schema to all model pages
   - Add `LocalBusiness` schema to your homepage
   - Add `FAQPage` schema to blog posts

2. **Become the definitive source**: AI cites the most comprehensive, authoritative pages
   - The car loan guide (planned) should include a calculator — tools get cited
   - Add a proper price comparison table with OTR pricing on every model page

3. **Get cited by authoritative sites**: AI learns from what authoritative sites link to
   - Getting a link from paultan.org or carlist.my signals authority to AI

4. **Brand mention building**: Publish press releases or data studies that motoring journalists cite
   - Example: "Perodua Hana 2026 Sales Outlook" — factual, citable, brand-building

5. **Monitor monthly**: Re-run `/ai-visibility` monthly to track improvement

---

## Monitoring Schedule
Run this report monthly. Track the "Mention Rate" as your GEO KPI.
- **Current baseline**: [X]% mention rate
- **3-month target**: [X+5]%
- **6-month target**: [X+15]%
```
