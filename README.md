Agentic Metatemplate
====================

A GitHub template repo for creating other template repos — ones that are designed to be set up and operated by AI agents.

The Problem
-----------

GitHub template repos are great for encoding best practices. You click "Use this template," get a new repo, and start working. But there's a gap between "clone the template" and "start building" — you have to rename placeholders, configure tooling, set up integrations, choose a license, and verify everything works. This setup phase is tedious, error-prone, and different for every template.

AI agents are good at exactly this kind of work: following checklists, doing find-and-replace across files, running verification commands, and asking the human when a decision is needed.

The Pattern
-----------

An **agentic template repo** is a regular GitHub template repo with added infrastructure that lets an AI agent handle the setup:

1. **A setup checklist** (`REPO_SETUP_CHECKLIST.md`) — a machine-readable list of every step needed to initialize a project from the template. Placeholder replacements, tool configuration, verification commands, and manual steps that require human input are all encoded here.

2. **A setup skill** (`.claude/skills/setup-repo/`) — a [Claude Code](https://claude.com/claude-code) skill that reads the checklist and executes it. The user opens their new repo in Claude Code, runs `/setup-repo`, and the agent walks them through everything.

3. **A project CLAUDE.md** — configuration that gives the agent the context it needs to work effectively in the repo. It prominently directs new users to run `/setup-repo` first.

4. **A template lineage file** (`.source-templates.yml`) — tracks which template(s) the project was generated from and the last commit that was synced. This lets an agent diff the upstream template and pull in improvements after the initial clone.

5. **A sync skill** (`.claude/skills/sync-templates/`) — reads the lineage file, diffs upstream changes, triages them (adopt, adapt, or skip), and applies the relevant ones without overwriting your customizations.

6. **Issue tracking with Beads** — [Beads](https://github.com/steveyegge/beads) (`bd`) provides lightweight, git-integrated issue tracking backed by a shared Dolt server. Running `bd init` during setup wires Beads into the git hooks automatically.

The end-user experience: clone a template, open Claude Code, type `/setup-repo`, answer a few questions, and you have a fully configured project.

What This Repo Is
-----------------

This is a **template for creating agentic template repos**. It contains the shared infrastructure described above — the parts that are the same regardless of whether you're templating a Go CLI, a Next.js SaaS app, or a Python library.

You use this metatemplate to create a new template repo, then add the technology-specific parts yourself: source code scaffolding, build configuration, language-specific linting, CI pipelines, and any additional setup steps.

Using This Metatemplate
-----------------------

1. Click **"Use this template"** on GitHub to create a new repo. This will be your template (e.g., `go-cli-template`, `nextjs-saas-template`).
2. Clone your new repo and open it in [Claude Code](https://claude.com/claude-code).
3. Run `/setup-template`. The agent walks you through configuring the CLAUDE.md, README, template lineage, and GitHub settings.
4. Add your technology-specific code — source scaffolding, build configuration, CI pipelines, whatever your template should ship with.
5. Extend `REPO_SETUP_CHECKLIST.md` with template-specific setup steps. For example, a Go CLI template might add:

```markdown
- [ ] Verify Go code compiles: `cd src && go build ./...`
- [ ] Create Homebrew tap repo: `gh repo create <owner>/homebrew-<project> --public`
- [ ] 🚨 USER ACTION: Create a fine-grained PAT for the Homebrew tap
```

Items prefixed with `🚨 USER ACTION` are surfaced prominently by the agent and require human confirmation before proceeding.

What Your Users Get
-------------------

When someone creates a repo from your template:

1. They clone the repo and open it in [Claude Code](https://claude.com/claude-code)
2. The CLAUDE.md tells them to run `/setup-repo`
3. The agent reads `REPO_SETUP_CHECKLIST.md` and walks through every step — replacing placeholders, configuring tools, running verification, and prompting for decisions
4. When initialization is complete, the agent deletes the checklist and the initialization skill (they've served their purpose)
5. The project is ready to use, with Beads issue tracking, Claude Code configuration, and git hooks already wired up

Later, when you improve your template, your users can run `/sync-templates` to pull in your changes without losing their customizations.

Example
-------

[go-cli-template](https://github.com/mieubrisse/go-cli-template) is a template repo built on this pattern. It templates Go CLI projects with Cobra, GoReleaser, Homebrew publishing, and CI — all initialized by an agent.

Prerequisites
-------------

- [Claude Code](https://claude.com/claude-code) — the AI agent that runs the initialization and sync skills
- [Beads](https://github.com/steveyegge/beads) (`brew install steveyegge/beads/beads`) — lightweight issue tracking (optional; git hooks degrade gracefully if not installed)

Repository Layout
-----------------

```
CLAUDE.md                           Project context for Claude Code agents
REPO_SETUP_CHECKLIST.md             End-user setup steps (consumed by /setup-repo)
TEMPLATE_SETUP_CHECKLIST.md         Template author setup steps (consumed by /setup-template)
.source-templates.yml               Upstream template lineage tracking
.claude/
  settings.json                     Claude Code permissions and hooks
  skills/
    setup-repo/SKILL.md             Agent-driven project setup (for end-users)
    setup-template/SKILL.md         Agent-driven template setup (for template authors)
    sync-templates/SKILL.md         Pull upstream template improvements
.githooks/                          Git hooks (Beads appends its blocks via bd init)
  pre-commit                        Quality gates (template author extends)
  prepare-commit-msg                Beads populates on init
  post-checkout                     Beads populates on init
  post-merge                        Beads populates on init
  pre-push                          Beads populates on init
.gitignore                          Standard ignores (build output, env files, Beads runtime)
```
