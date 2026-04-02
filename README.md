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

1. **A setup checklist** (`SETUP_CHECKLIST.md`) — a machine-readable list of every step needed to initialize a project from the template. Placeholder replacements, tool configuration, verification commands, and manual steps that require human input are all encoded here.

2. **An initialization skill** (`.claude/skills/initialize-repo/`) — a [Claude Code](https://claude.com/claude-code) skill that reads the checklist and executes it. The user opens their new repo in Claude Code, runs `/initialize-repo`, and the agent walks them through everything.

3. **A project CLAUDE.md** — configuration that gives the agent the context it needs to work effectively in the repo. It prominently directs new users to run `/initialize-repo` first.

4. **A template lineage file** (`.source-templates.yml`) — tracks which template(s) the project was generated from and the last commit that was synced. This lets an agent diff the upstream template and pull in improvements after the initial clone.

5. **A sync skill** (`.claude/skills/sync-templates/`) — reads the lineage file, diffs upstream changes, triages them (adopt, adapt, or skip), and applies the relevant ones without overwriting your customizations.

6. **Issue tracking with Beads** — [Beads](https://github.com/steveyegge/beads) (`bd`) provides lightweight, git-integrated issue tracking backed by a shared Dolt server. Git hooks handle Beads integration gracefully — if `bd` isn't installed, hooks skip Beads and continue without blocking commits.

The end-user experience: clone a template, open Claude Code, type `/initialize-repo`, answer a few questions, and you have a fully configured project.

What This Repo Is
-----------------

This is a **template for creating agentic template repos**. It contains the shared infrastructure described above — the parts that are the same regardless of whether you're templating a Go CLI, a Next.js SaaS app, or a Python library.

You use this metatemplate to create a new template repo, then add the technology-specific parts yourself: source code scaffolding, build configuration, language-specific linting, CI pipelines, and any additional setup steps.

Using This Metatemplate
-----------------------

### 1. Create your template repo

Click **"Use this template"** on GitHub to create a new repo. This will be your template (e.g., `go-cli-template`, `nextjs-saas-template`).

### 2. Add your technology-specific code

This is the creative part. Add whatever scaffolding, source code, build configuration, and tooling your template should ship with. There are no constraints on what you add — the metatemplate provides the agent infrastructure, and you provide the domain expertise.

### 3. Extend the setup checklist

Open `SETUP_CHECKLIST.md`. It comes with base items that apply to all projects (placeholder replacement, Beads initialization, license selection, git hooks). Add items specific to your template. For example, a Go CLI template might add:

```markdown
- [ ] Verify Go code compiles: `cd src && go build ./...`
- [ ] Create Homebrew tap repo: `gh repo create <owner>/homebrew-<project> --public`
- [ ] 🚨 USER ACTION: Create a fine-grained PAT for the Homebrew tap
```

Items prefixed with `🚨 USER ACTION` are surfaced prominently by the initialization agent and require the human to confirm completion before proceeding.

### 4. Customize CLAUDE.md

Replace the placeholder sections in `CLAUDE.md` with your template's actual project structure, build commands, and conventions. Keep the Beads and sync-templates sections — they apply to all projects. The `/initialize-repo` callout at the top should stay as-is; it's what end-users see when they first open the project.

### 5. Customize the Claude Code settings

Edit `.claude/settings.json` to add permissions for your template's tooling. The base settings include Beads permissions and standard file access. Add whatever your template needs — build tools, linters, language runtimes.

### 6. Write your README

Replace this README with one that describes your template. Include a callout block at the top (like the one in this repo) that tells users how to get started with `/initialize-repo`. See [go-cli-template](https://github.com/mieubrisse/go-cli-template) for an example.

### 7. Update the template lineage

Edit `.source-templates.yml` and set `lastSyncedCommit` to the current HEAD of this metatemplate repo. This lets your template pull in future improvements from the metatemplate via `/sync-templates`.

### 8. Mark as a template

In your repo's GitHub settings (Settings → General), check the **Template repository** box.

What Your Users Get
-------------------

When someone creates a repo from your template:

1. They clone the repo and open it in [Claude Code](https://claude.com/claude-code)
2. The CLAUDE.md tells them to run `/initialize-repo`
3. The agent reads `SETUP_CHECKLIST.md` and walks through every step — replacing placeholders, configuring tools, running verification, and prompting for decisions
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
SETUP_CHECKLIST.md                  Initialization steps (consumed by /initialize-repo)
.source-templates.yml               Upstream template lineage tracking
.claude/
  settings.json                     Claude Code permissions and hooks
  skills/
    initialize-repo/SKILL.md        Agent-driven project initialization
    sync-templates/SKILL.md         Pull upstream template improvements
.githooks/                          Git hooks with Beads integration
  pre-commit                        Quality gates (template author extends)
  prepare-commit-msg                Beads issue linking
  post-checkout                     Beads sync
  post-merge                        Beads sync
  pre-push                          Beads sync
.gitignore                          Standard ignores (build output, env files, Beads runtime)
```
