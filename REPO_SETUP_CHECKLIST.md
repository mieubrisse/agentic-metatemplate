Repo Setup Checklist
====================

This checklist is consumed by the `/setup-repo` skill. Run that skill instead of following this manually.

- [ ] Get GitHub owner and project name from the user
- [ ] Find-and-replace `owner-replaceme` with actual GitHub owner across all files
- [ ] Find-and-replace `project-replaceme` with actual project name across all files
- [ ] Configure git hooks: `git config core.hooksPath .githooks`
- [ ] Initialize beads: `bd init --shared-server --prefix <project-name>`
- [ ] Choose a license and create the LICENSE file
- [ ] Update CLAUDE.md: replace the content between `<!-- BEGIN TEMPLATE -->` and `<!-- END TEMPLATE -->` markers with project-specific context (tech stack, commands, conventions, project structure)
- [ ] Update `.source-templates.yml`: find the entry containing `owner-replaceme`/`project-replaceme` and set its `lastSyncedCommit` to the current HEAD of the template repo. Update `guidance` if needed. Leave other entries unchanged.
- [ ] Remove the template usage callout from the top of README.md
- [ ] Verify the project builds successfully
- [ ] Delete this file (REPO_SETUP_CHECKLIST.md)
- [ ] Delete `.claude/skills/setup-repo/`
- [ ] Commit and push all changes
