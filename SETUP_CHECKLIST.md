Setup Checklist
===============

This checklist is consumed by the `/initialize-repo` skill. Run that skill instead of following this manually.

- [ ] Get GitHub owner and project name from the user
- [ ] Find-and-replace `owner-replaceme` with actual GitHub owner across all files
- [ ] Find-and-replace `project-replaceme` with actual project name across all files
- [ ] Initialize beads: `bd init --shared-server --prefix <project-name>`
- [ ] Choose a license and create the LICENSE file
- [ ] Configure git hooks: `git config core.hooksPath .githooks`
- [ ] Update CLAUDE.md: replace the placeholder description with project-specific context (tech stack, commands, conventions, project structure)
- [ ] Update `.source-templates.yml`: set `lastSyncedCommit` to the current HEAD of the template repo
- [ ] Remove the template usage callout from the top of README.md
- [ ] Verify the project builds successfully
- [ ] Delete this file (SETUP_CHECKLIST.md)
- [ ] Delete `.claude/skills/initialize-repo/`
- [ ] Commit and push all changes
