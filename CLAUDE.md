# SEO Command Center — peroduahana.com

## Site Context
- **Website**: peroduahana.com
- **Business**: Perodua car dealership / information site targeting Malaysian consumers
- **Language**: Primarily Bahasa Melayu + English (bilingual)
- **Market**: Malaysia (location_code: 2458 for DataForSEO)

## MCP Servers Connected
- **Google Search Console**: property `sc-domain:peroduahana.com`, GA4 property ID `540539228`
- **Google Analytics**: GA4 property ID `540539228`
- **DataForSEO**: credentials from env (m.hasif802@gmail.com)

## Custom Skills (slash commands)

### Tier 1 — Core reporting
| Command | Purpose |
|---|---|
| `/weekly-seo-report` | Full GSC + GA4 + DataForSEO weekly report, saves to `peroduahana.report.YYYY-MM-DD.md` |
| `/ctr-rescue` | Finds position 4–20 queries with low CTR, fetches SERP data, rewrites title/meta |
| `/keyword-gaps` | Finds high-value keywords not yet ranking for, outputs prioritised content calendar |

### Tier 2 — Content & growth
| Command | Usage | Purpose |
|---|---|---|
| `/content-brief` | `/content-brief [keyword]` | Full article brief: intent, SERP analysis, competitor structure, outline |
| `/competitor-spy` | `/competitor-spy [domain]` | Keyword gap + backlink gap vs a specific competitor |
| `/backlink-audit` | `/backlink-audit` | Full link profile: authority, new/lost links, spam flags, action plan |

### Tier 3 — Advanced
| Command | Usage | Purpose |
|---|---|---|
| `/ai-visibility` | `/ai-visibility [brand]` | ChatGPT/LLM mention tracking (GEO) — is peroduahana.com in AI answers? |
| `/serp-tracker` | `/serp-tracker kw1, kw2, ...` | Live rank check + week-over-week movement, saves snapshot for trending |
| `/technical-audit` | `/technical-audit` | On-page crawl + Lighthouse + GSC indexing issues + schema gaps |

## DataForSEO Defaults
- Always use `location_code: 2458` (Malaysia) unless otherwise specified
- Language: `en` for English queries, `ms` for Malay queries
- For SERP calls: `depth: 10` is sufficient for most analysis

## Key Findings (as of 2026-06-08)
- Site has 0 organic clicks — still in early SEO phase
- Near-page-1 rankings: `harga kereta alza baru 2026` (pos 9), `kelebihan perodua traz` (pos 10.5)
- Biggest content gap: `cara apply loan kereta` (pos 28), needs a dedicated guide page
- GTM debug params (`gtm_latency`, `gtm_debug`) are polluting GA4 landing page data
