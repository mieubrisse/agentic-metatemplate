project-replaceme
=================

> **First time here?** Run `/initialize-repo` in [Claude Code](https://claude.com/claude-code) to set up this project. The skill walks you through every setup step automatically.

A [brief project description — template author replaces this paragraph with what the project does, who it's for, and why it exists].

This file contains the non-obvious context an agent needs to work effectively in this repo — conventions, build commands, and workflow rules that cannot be inferred from reading the code alone.

Project Structure
-----------------

```
[Template author: replace this with the actual project layout.
Show the directory tree with brief annotations for each directory's purpose.]
```

Building and Running
--------------------

[Template author: add the build, test, and run commands for this project. Include any prerequisites or environment setup needed.]

Required Workflows
------------------

### New features

Invoke `/brainstorm` before starting any new feature work. This ensures the design is explored and validated before code is written, preventing wasted effort on approaches that miss requirements or have better alternatives.

### README changes

Invoke `/technical-writer` when modifying the README. The skill produces documentation that is clear, well-structured, and consistent with the project's voice.

Issue Tracking
--------------

This project uses **bd** (Beads) for issue tracking, configured in shared Dolt server mode. The server runs at `127.0.0.1` and auto-starts when needed.

```bash
bd prime          # Load workflow context
bd ready          # Find unblocked work
bd create "Title" # Create an issue
bd close <id>     # Complete an issue
```

Run `bd prime` for full workflow details.

If `bd` is not installed, issue tracking commands will fail. The git hooks handle a missing `bd` gracefully — they skip beads integration and continue — so a missing `bd` will not block commits. To install beads:

```bash
brew install steveyegge/beads/beads
```

Syncing with Upstream Templates
-------------------------------

This project tracks its upstream template(s) via `.source-templates.yml`. Invoke `/sync-templates` to pull in improvements from the template repos this project was generated from. The skill diffs upstream changes, triages them for relevance, and applies the ones that fit — without overwriting your customizations.
