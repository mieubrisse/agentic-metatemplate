---
name: sync-templates
description: >-
  Syncs the current project with its upstream template repositories. Reads
  .source-templates.yml, diffs each template from lastSyncedCommit to HEAD,
  identifies improvements to incorporate, and applies them with verification.
  Invoke when the user wants to pull in upstream template improvements.
---

Sync Upstream Templates
=======================

This skill pulls improvements from the template repositories that this project was generated from. The project may be derived from multiple templates — each is tracked independently.

Manifest Format
---------------

`.source-templates.yml` is a YAML list at the project root:

```yaml
- repo: github.com/owner/template-a
  lastSyncedCommit: abc123def456
  guidance: >-
    Focus on CI and build config changes. Ignore example components
    and placeholder content — this project has its own.

- repo: github.com/owner/template-b
  lastSyncedCommit: 789abc012def
  guidance: >-
    This template provides the auth middleware. Adopt security fixes
    and Clerk upgrades. Skip styling changes.
```

Workflow
--------

### 1. Load manifest and generate diffs

Read `.source-templates.yml` from the project root. If the file is missing or empty, tell the user and stop.

For each template entry:

**a) Access the template repo.** Check the AgenC repo library at `/Users/odyssey/.agenc/repos/` for a local copy. If unavailable, clone the repo to `$TMPDIR`. Fetch the latest changes to ensure the repo is up to date.

**b) Write the upstream diff to a file.** Generate the diff from `lastSyncedCommit` to the current HEAD and save it to `$TMPDIR/sync-<repo-name>.diff`. This file is the shared artifact that both the triage/execution phase and the verification phase read from.

```bash
git -C <template-repo-path> log --oneline <lastSyncedCommit>..HEAD > $TMPDIR/sync-<repo-name>.log
git -C <template-repo-path> diff <lastSyncedCommit>..HEAD > $TMPDIR/sync-<repo-name>.diff
```

If `lastSyncedCommit` is `TODO` or empty, show the user the template's recent tags and release history, and ask which commit to start from.

If the diff is very large (100+ files or months of commits), summarize at the commit level first and let the user choose which areas to focus on, rather than triaging every file change.

### 2. Triage each template

For each template, read its diff file and the `guidance` field from the manifest. The guidance calibrates which upstream changes are relevant — downstream repos diverge from templates, and the guidance describes what to focus on and what to ignore.

Classify each change in the diff:

- **Adopt** — the change improves infrastructure, fixes a bug, or upgrades tooling in a way that applies regardless of downstream customization (CI improvements, security fixes, dependency updates, build config)
- **Adapt** — the change has value but needs modification to fit the downstream project's structure (a pattern change that applies but to different files/names)
- **Skip** — the change is template-specific content that the downstream project has intentionally diverged from (placeholder content, example components, template-only features)

When in doubt about whether a change should be adopted, adapted, or skipped — ask the user. Present the specific change, explain what it does, and let them decide.

**Present the plan.** Show the user a numbered checklist of changes to incorporate, grouped by template. For each item, include:

- What the upstream change does
- Whether it's being adopted as-is or adapted
- Which files in the current project are affected

Wait for user confirmation before proceeding.

### 3. Execute and commit per template

Work through each approved change for one template. After applying all changes for that template:

- Update `lastSyncedCommit` in `.source-templates.yml` to the template's current HEAD
- Verify the project builds (check `package.json` scripts or the project's CLAUDE.md for the correct build command)
- Verify types pass if applicable
- Commit: `chore: sync with upstream template <repo-name>`

Committing per template ensures that if a later template sync fails, the earlier successful syncs are preserved.

Then move to the next template.

### 4. Verification loop

After all templates are processed, run a convergent verification cycle. **Maximum 3 rounds** — if issues persist after 3 rounds, present the remaining issues to the user for manual resolution.

1. Spawn a subagent with this prompt, listing the diff file paths:

   > You are a Template Sync Verification Agent. The project just synced changes from its upstream templates. The upstream diffs are saved as files — read them to see what changed upstream.
   >
   > For each diff file listed below:
   > 1. Read the diff file to understand what changed upstream
   > 2. Compare each upstream change against the current project state
   > 3. Identify any changes that were supposed to be incorporated but were missed, applied incorrectly, or only partially applied
   > 4. Check that the project builds and types pass
   >
   > Diff files:
   > - `<repo>`: `$TMPDIR/sync-<repo-name>.diff` (commit log: `$TMPDIR/sync-<repo-name>.log`)
   > - (repeat for each template)
   >
   > Report your findings as a list of specific issues. If everything looks correct, say "No issues found." Be thorough — check file by file.

2. If the subagent finds issues, fix them and commit the fixes.
3. Repeat (up to 3 total rounds) until the subagent reports no issues.

Judgment Calls
--------------

Template syncing is inherently a judgment-heavy process. The downstream project will have diverged from the template, and not every upstream change belongs in every downstream project. Prioritize:

- **Security fixes and vulnerability patches** — always adopt
- **Build and CI improvements** — usually adopt
- **Framework/dependency upgrades** — adopt if compatible, ask if major version
- **New features or patterns** — ask the user
- **Content or styling changes** — usually skip (the downstream project has its own content)

When a template change touches a file that the downstream project has heavily customized, read both versions carefully. Apply the *intent* of the upstream change rather than blindly merging the diff. The goal is to get the benefit of the improvement without losing downstream customizations.
