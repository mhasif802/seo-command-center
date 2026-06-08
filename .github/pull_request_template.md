## What does this PR do?

<!-- One paragraph summary of the change -->

## Type of change

- [ ] New skill (new `.claude/commands/*.md` file)
- [ ] Skill improvement (updated prompt or data schema)
- [ ] Dashboard update (changes to `output/dashboard.html`)
- [ ] Bug fix
- [ ] Documentation

## Checklist

- [ ] The skill file follows the naming convention `kebab-case.md`
- [ ] If adding a new skill with a dashboard tab: the data block (`window.XYZ_DATA`) and its `/* START */` / `/* END */` markers are added to `dashboard.html`
- [ ] If changing a data schema: the schema comment in `dashboard.html` is updated to match
- [ ] The `CLAUDE.md` skill table is updated if a new command was added
- [ ] Tested by running the skill in Claude Code and verifying the dashboard updates correctly
- [ ] No credentials, `.env`, or `.mcp.json` files included

## Screenshots (if dashboard changes)

<!-- Before / after screenshots of the dashboard tab -->
