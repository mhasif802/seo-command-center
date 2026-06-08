# SEO Command Center

**9 Claude Code slash commands that turn Google Search Console, Google Analytics, and DataForSEO into a live SEO intelligence system — with a self-updating HTML dashboard.**

[![License: MIT](https://img.shields.io/badge/License-MIT-6366f1.svg)](LICENSE)
[![Built for Claude Code](https://img.shields.io/badge/Built%20for-Claude%20Code-000000?logo=anthropic)](https://claude.ai/code)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-10b981.svg)](CONTRIBUTING.md)

---

## Overview

Type a slash command in Claude Code. Claude pulls live data from three MCP servers, writes an analysis report, and updates the relevant tab of a local HTML dashboard — all in one step.

No dashboards to log into. No manual CSV exports. No recurring SaaS subscriptions.

```
/weekly-seo-report          → Overview tab refreshed
/ctr-rescue                 → CTR Rescue tab with rewritten titles + metas
/keyword-gaps               → Keyword Gaps tab with content calendar
/serp-tracker alza 2026, traz  → SERP Tracker tab with ↑↓ movement
/backlink-audit             → Backlinks tab with new/lost links
/technical-audit            → Technical tab with Lighthouse scores
/ai-visibility              → AI Visibility tab (GEO / LLM mentions)
/content-brief [keyword]    → Content Brief tab with full article outline
/competitor-spy [domain]    → Competitor tab with keyword gap analysis
```

---

## Dashboard

A single `output/dashboard.html` file — open it once in a browser, keep it pinned. Every skill run updates its tab. Refresh to see the latest data.

**9 tabs, one file, no server required.**

| Tab | Updated by | Shows |
|---|---|---|
| Overview | `/weekly-seo-report` | KPIs, trend charts, top queries, authority score, priority actions |
| CTR Rescue | `/ctr-rescue` | Side-by-side current vs rewritten title + meta for each opportunity |
| Keyword Gaps | `/keyword-gaps` | Content calendar by cluster, top 10 articles sorted by win score |
| SERP Tracker | `/serp-tracker` | Live rankings with ↑↓ movement vs previous snapshot |
| Backlinks | `/backlink-audit` | Authority ring, new/lost links, top referring domains |
| Technical | `/technical-audit` | Lighthouse score circles, pages audit, issues by severity |
| AI Visibility | `/ai-visibility` | ChatGPT response cards, LLM mention rate, GEO action plan |
| Content Brief | `/content-brief` | Keyword stats, recommended title/meta, full article outline |
| Competitor | `/competitor-spy` | Head-to-head comparison, keyword gaps, "how to beat" notes |

---

## Requirements

- [Claude Code](https://claude.ai/code) installed
- A [Google Cloud](https://console.cloud.google.com) project with the Search Console API enabled (for the GSC MCP)
- Google Analytics 4 property
- [DataForSEO](https://dataforseo.com) account (free trial available)
- Node.js ≥ 18 (for the DataForSEO MCP via `npx`)
- Python ≥ 3.10 (for the GSC MCP server)

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/seo-command-center.git
cd seo-command-center
```

### 2. Install the Google Search Console MCP server

```bash
cd mcp-gsc
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
cd ..
```

### 3. Add your credentials

```bash
cp .env.example .env
cp .mcp.json.example .mcp.json
```

Edit `.env`:
```bash
GSC_OAUTH_CLIENT_SECRETS_FILE=./client_secret.json
GA4_PROPERTY_ID=your_property_id_here
DATAFORSEO_USERNAME=your@email.com
DATAFORSEO_PASSWORD=your_api_password_here
```

Edit `.mcp.json` — fill in your GA4 property ID and DataForSEO credentials. The DataForSEO password is your **API password** (generate it in the DataForSEO dashboard, it is different from your login password).

Copy your Google OAuth client secret JSON file into the project root:
```bash
cp ~/Downloads/client_secret_XXXX.json ./client_secret.json
```

### 4. Authenticate with Google (first time only)

```bash
cd mcp-gsc && source venv/bin/activate
python gsc_server.py --authenticate
cd ..
```

Follow the OAuth browser prompt. Your token is saved locally and reused automatically.

### 5. Update `CLAUDE.md` for your site

Open `CLAUDE.md` and replace the site-specific values:

```markdown
- **Website**: yoursite.com
- **GSC property**: sc-domain:yoursite.com   ← copy exactly from Search Console
- **GA4 property ID**: YOUR_PROPERTY_ID
- **Market**: [your country + DataForSEO location_code]
```

Find your DataForSEO location code at [dataforseo.com/location-codes](https://dataforseo.com/location-codes).

### 6. Open in Claude Code

```bash
claude .
```

Claude Code detects `.mcp.json` and connects all three MCP servers on startup. You should see them listed in green. Then run your first skill:

```
/weekly-seo-report
```

Open `output/dashboard.html` in your browser.

---

## Skills Reference

### `/weekly-seo-report`

Pulls GSC + GA4 + DataForSEO into a weekly snapshot. Saves `yoursite.report.YYYY-MM-DD.md` and updates the Overview tab.

Run every Monday morning.

---

### `/ctr-rescue`

Scans the last 28 days of GSC data for queries ranking position 4–20 with low CTR. For each, it fetches the live SERP via DataForSEO to see what competitors' titles look like — then rewrites yours.

Updates the **CTR Rescue** tab with before/after title + meta cards.

---

### `/keyword-gaps`

Compares your GSC keyword footprint against your top 3 competitors (via DataForSEO Labs). Scores every gap keyword by `(volume / 100) × (100 - difficulty)` and groups them into content clusters.

Updates the **Keyword Gaps** tab with a prioritised content calendar.

---

### `/serp-tracker [kw1], [kw2], ...`

Fetches live SERP positions for your target keywords. On the first run it saves a baseline snapshot; on subsequent runs it shows week-over-week movement (↑ ↓ →).

```bash
/serp-tracker harga kereta alza 2026, kelebihan perodua traz, cara apply loan kereta
```

Saves `serp-watchlist.txt`, `serp-snapshot.json`, and `serp-history.jsonl`.

---

### `/backlink-audit`

Pulls your full backlink profile from DataForSEO: authority score, referring domains, new/lost links (last 30 days), spam score, anchor text distribution.

Updates the **Backlinks** tab.

---

### `/technical-audit`

Crawls your key pages with DataForSEO On-Page, runs Lighthouse via DataForSEO, and cross-references GSC indexing data. Flags issues by severity with copy-paste JSON-LD schema templates.

Updates the **Technical** tab.

---

### `/ai-visibility [brand]`

Uses DataForSEO's LLM mention APIs to check how your brand appears in ChatGPT and other AI responses. Measures your **Generative Engine Optimisation (GEO)** baseline — how often AI assistants mention or recommend your site.

```bash
/ai-visibility
/ai-visibility "Perodua Hana"
```

Updates the **AI Visibility** tab.

---

### `/content-brief [keyword]`

Give it a keyword and get a complete article brief: SERP analysis, competitor H2 structure, word count benchmarks, recommended title + meta, full outline with supporting keywords mapped to each section.

```bash
/content-brief cara apply loan kereta perodua
/content-brief harga perodua myvi 2026
```

Updates the **Content Brief** tab with the latest brief.

---

### `/competitor-spy [domain]`

Deep analysis of a competitor's keyword profile. Finds every keyword they rank for that you don't, scores the gaps, analyses their top 5 traffic pages, and shows their backlink advantage.

```bash
/competitor-spy wapcar.my
/competitor-spy paultan.org
```

Updates the **Competitor** tab.

---

## Recommended Workflow

```
Week 1 (baseline)
  /technical-audit          → fix critical issues first
  /serp-tracker kw1, kw2    → save your starting positions
  /backlink-audit           → know your link baseline

Every Monday
  /weekly-seo-report        → your weekly pulse check
  /serp-tracker             → track rank movement

Content planning
  /keyword-gaps             → find what to write
  /content-brief [kw]       → one per planned article

Monthly
  /backlink-audit           → track link building
  /ai-visibility            → track GEO growth

Quarterly
  /competitor-spy [domain]  → find new gaps
  /technical-audit          → catch regressions
```

---

## How the dashboard updates

Each skill file (`.claude/commands/*.md`) instructs Claude to:

1. Collect data from the relevant MCPs
2. Write a dated Markdown report (for the `peroduahana.report.*.md` archive)
3. Use the `Edit` tool to replace its own `window.XYZ_DATA` block inside `output/dashboard.html`

The HTML file has **9 clearly delimited data blocks**, one per skill:

```
/* ── OVERVIEW DATA START (set by /weekly-seo-report) ── */
window.OVERVIEW_DATA = { ... };
/* ── OVERVIEW DATA END ── */
```

Each skill only touches its own block. All other tabs remain intact. Refresh your browser tab to see the new data.

---

## Project Structure

```
seo-command-center/
├── .claude/
│   └── commands/               # All 9 SEO skills (slash commands)
│       ├── weekly-seo-report.md
│       ├── ctr-rescue.md
│       ├── keyword-gaps.md
│       ├── serp-tracker.md
│       ├── backlink-audit.md
│       ├── technical-audit.md
│       ├── ai-visibility.md
│       ├── content-brief.md
│       └── competitor-spy.md
├── .github/
│   ├── ISSUE_TEMPLATE/
│   └── pull_request_template.md
├── mcp-gsc/                    # Google Search Console MCP server
├── output/
│   └── dashboard.html          # Live SEO dashboard (updated by skills)
├── .mcp.json.example           # MCP config template
├── .env.example
├── .gitignore
├── CLAUDE.md                   # Site context auto-loaded by Claude Code
├── CONTRIBUTING.md
└── README.md
```

Runtime files created by skills (gitignored):
```
├── .mcp.json                   # Your credentials (never committed)
├── client_secret.json          # Google OAuth (never committed)
├── serp-watchlist.txt          # Your keyword watchlist
├── serp-snapshot.json          # Latest SERP positions
├── serp-history.jsonl          # Full historical SERP data
└── *.report.YYYY-MM-DD.md      # Weekly reports archive
```

---

## Adapting for your own site

1. Replace all references to `peroduahana.com` in `CLAUDE.md` with your domain
2. Update the GSC property string (`sc-domain:yoursite.com` or `https://yoursite.com/`)
3. Update the GA4 property ID in `.mcp.json`
4. Change the `location_code` in `CLAUDE.md` if you're not targeting Malaysia (code `2458`)
5. Run `/weekly-seo-report` — the dashboard header will reflect your site automatically

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## License

[MIT](LICENSE)
