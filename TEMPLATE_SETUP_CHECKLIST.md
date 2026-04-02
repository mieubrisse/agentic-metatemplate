Template Setup Checklist
========================

This checklist is consumed by the `/setup-template` skill. Run that skill instead of following this manually.

This checklist is for **template authors** — people who created a repo from the agentic-metatemplate and want to turn it into their own template.

- [ ] Get the template's name, purpose, and technology stack from the user
- [ ] Replace the `<!-- BEGIN METATEMPLATE -->` ... `<!-- END METATEMPLATE -->` block in CLAUDE.md with content describing the new template (project structure, build commands, conventions, editing guidelines)
- [ ] Replace README.md with one describing the new template — include a callout at the top directing users to run `/setup-repo`, and explain what the template provides
- [ ] Update `.source-templates.yml`: set the existing metatemplate entry's `lastSyncedCommit` to the current HEAD of the metatemplate repo, then add a second entry for the new template itself using `owner-replaceme`/`project-replaceme` placeholders (end-users will replace these via `/setup-repo`)
- [ ] Add template-specific items to `REPO_SETUP_CHECKLIST.md` (build verification, tool setup, etc.)
- [ ] Add template-specific permissions to `.claude/settings.json` (build tools, linters, language runtimes)
- [ ] Add quality checks to `.githooks/pre-commit` (linting, formatting, tests)
- [ ] 🚨 USER ACTION: Mark the repo as a "Template repository" on GitHub (Settings → General → Template repository checkbox)
- [ ] Delete this file (TEMPLATE_SETUP_CHECKLIST.md)
- [ ] Delete `.claude/skills/setup-template/`
- [ ] Commit and push all changes
