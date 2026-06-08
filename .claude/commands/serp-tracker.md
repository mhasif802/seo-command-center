# SERP Tracker

Fetch live Google SERP rankings for a list of target keywords and show current positions, top 3 competitors, and movement vs. the last saved snapshot.

Usage: `/serp-tracker [keyword1], [keyword2], [keyword3]...`
Example: `/serp-tracker harga kereta alza baru 2026, kelebihan perodua traz, cara apply loan kereta`

If no keywords are provided, use the default watchlist stored in `serp-watchlist.txt` (or create the file if it doesn't exist by asking the user for their initial keyword list).

## Steps

### 1. Parse keywords
- If `$ARGUMENTS` is provided: split by comma, trim whitespace, deduplicate. Use these as the keyword list.
- If `$ARGUMENTS` is empty: read keywords from `/root/seo-command-center/serp-watchlist.txt` (one keyword per line). If the file doesn't exist, ask the user to provide their target keywords.

### 2. Load previous snapshot (if exists)
Check if `/root/seo-command-center/serp-snapshot.json` exists. If it does, read it — it contains the previous rank for each keyword. This enables week-over-week movement tracking.

Format of snapshot file:
```json
{
  "last_run": "YYYY-MM-DD",
  "rankings": {
    "keyword 1": { "position": 9, "url": "/model/alza/" },
    "keyword 2": { "position": 10, "url": "/model/traz/" }
  }
}
```

### 3. Fetch live SERP for each keyword
For each keyword, call `mcp__dataforseo__serp_organic_live_advanced`:
- location_code: 2458 (Malaysia)
- language_code: en or ms (detect from keyword — if Malay words present, use ms)
- depth: 20 (check top 20 to find your position even if not in top 10)
- keyword: [keyword]

From the results:
- Find peroduahana.com's position (scan all 20 results for domain match)
- Record position, URL, title tag as it appears in SERP
- Record the #1, #2, #3 competitors (domains + titles)
- Note any SERP features (featured snippet, PAA, video, local pack)

### 4. Calculate movement
For each keyword, compare current position to previous snapshot:
- If no previous data: mark as "New"
- If position improved: show "↑ +[N] positions"
- If position worsened: show "↓ -[N] positions"
- If unchanged: show "→ No change"
- If not found in top 20: show "Not ranking (top 20)"

### 5. Save updated snapshot
Write the new snapshot to `/root/seo-command-center/serp-snapshot.json` with today's date and all current positions.

Also append a historical record to `/root/seo-command-center/serp-history.jsonl` (one JSON line per run, never overwritten — this builds a longitudinal dataset).

### 6. Output the tracker report

```
# SERP Tracker — peroduahana.com
Date: [today] | Previous snapshot: [last_run date or "None"]

---

## Rankings Summary

| Keyword | Position | Movement | URL Ranking | SERP Features |
|---|---|---|---|---|
| [keyword] | [pos] | ↑ +2 | /model/alza/ | PAA |
| [keyword] | [pos] | → No change | /model/traz/ | — |
| [keyword] | Not ranking (top 20) | New | — | Featured snippet (competitor) |

---

## Detailed Breakdown

### [Keyword 1]
**Your position**: [N] | **Movement**: [↑/↓/→ or New]
**Your ranking URL**: [URL] | **Your title in SERP**: [title]

| Rank | Domain | Title |
|---|---|---|
| 1 | [domain] | [title] |
| 2 | [domain] | [title] |
| 3 | [domain] | [title] |
| [your pos] | **peroduahana.com** | **[title]** |

SERP features: [list any features and who owns them]

[Repeat for each keyword]

---

## Opportunities & Alerts

**Climbing fast (up 3+ positions):**
[List keywords improving quickly]

**Dropping (down 3+ positions):**
[List keywords falling — investigate why]

**Near page 1 (positions 11–20):**
[Keywords just off page 1 — these are priority targets for optimisation]

**Lost rankings (was ranking, now not in top 20):**
[Alert: these need immediate attention]

---

## Recommended Actions
[3–5 specific actions based on the current data, e.g. "Keyword X dropped 4 spots — check if a competitor published new content recently"]
```

### 7. Update the HTML dashboard

Use the **Edit tool** to replace the block in `output/dashboard.html` between:
```
/* ── SERP DATA START (set by /serp-tracker) ── */
window.SERP_DATA = null;
/* ── SERP DATA END ── */
```

Replace `window.SERP_DATA = null;` with:
```javascript
window.SERP_DATA = {
  lastRun: "[YYYY-MM-DD]",
  prevRun: "[YYYY-MM-DD or null]",
  keywords: [
    {
      keyword: "harga kereta alza baru 2026",
      position: 9.0,
      prevPosition: 11.0,   // from snapshot, null if first run
      movement: 2.0,        // prevPosition - position (positive = improved)
      url: "/model/alza/",
      title: "Perodua Alza | Perodua Hana",
      competitors: [
        { domain: "wapcar.my", title: "..." },
        { domain: "paultan.org", title: "..." },
        { domain: "carlist.my", title: "..." },
      ],
    },
    // ... all tracked keywords
  ],
};
```
Print: `Dashboard updated → output/dashboard.html`

### 8. Watchlist management
If the user ran with explicit keywords (from `$ARGUMENTS`), ask at the end:
> "Would you like to save these keywords to your permanent watchlist? (yes/no)"

If yes, append any new keywords to `/root/seo-command-center/serp-watchlist.txt`.
