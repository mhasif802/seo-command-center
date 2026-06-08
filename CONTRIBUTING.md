# Contributing

Contributions are welcome — new skills, dashboard improvements, bug fixes, and docs.

## Types of contributions

- **New skill** — a new `.claude/commands/*.md` file adding a slash command
- **Skill improvement** — better prompts, additional data sources, improved output formatting
- **Dashboard panel** — new tab or visualisation in `output/dashboard.html`
- **Bug fix** — broken MCP call, wrong data schema, JS error in the dashboard
- **Docs** — clearer setup instructions, new workflow examples

## Getting started

1. Fork the repo and clone your fork
2. Follow the [Setup](README.md#setup) instructions with your own site credentials
3. Make your changes
4. Test the skill end-to-end: run it in Claude Code and verify the dashboard tab updates correctly
5. Open a pull request using the [PR template](.github/pull_request_template.md)

## Adding a new skill

1. Create `.claude/commands/your-skill-name.md`
2. Follow the structure of existing skills:
   - numbered steps
   - clear MCP tool names (`mcp__dataforseo__...`, `mcp__google-search-console__...`)
   - a final step that updates the HTML dashboard
3. Add a new `window.YOUR_DATA` block to `output/dashboard.html` with matching `/* START */` / `/* END */` markers
4. Add the render function and tab entry in the dashboard's JS
5. Update the skill table in `CLAUDE.md` and `README.md`

## Dashboard data schemas

Each skill writes to a named block in `dashboard.html`. The full schema for each block is documented in the large comment block near the top of the `<script>` section. Keep schemas backward compatible where possible — add new optional fields rather than removing existing ones.

## Code style

- Skill files: plain Markdown, imperative language ("Call X", "Extract Y", "Write Z")
- Dashboard JS: vanilla JS, no build step, no external dependencies beyond Chart.js CDN
- Keep the dashboard as a single self-contained HTML file

## Reporting bugs

Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.yml). Include the skill name, what MCP call failed (if applicable), and the error output from Claude Code.
