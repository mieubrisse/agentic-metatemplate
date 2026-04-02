---
name: setup-template
description: >-
  Transforms the agentic metatemplate into a specific template repo. Invoke after
  creating a repo from the agentic-metatemplate. Replaces metatemplate content with
  template-specific context, updates the README, and configures template lineage.
  Self-destructs after completion.
---

Setup Template
==============

This skill transforms the metatemplate into your own template repo.

Read `TEMPLATE_SETUP_CHECKLIST.md` and execute each item in order, checking them off as you go. The checklist is the single source of truth for all setup steps.

For items that require user input (template name, purpose, technology stack), ask the user and wait for their response before proceeding.

For items marked with `🚨 USER ACTION`, surface them clearly and wait for the user to confirm completion before continuing.

When replacing the CLAUDE.md content between the `<!-- BEGIN METATEMPLATE -->` and `<!-- END METATEMPLATE -->` markers, keep the sections outside the markers (Issue Tracking, Syncing with Upstream Templates) — they apply to all template repos. Write new content describing the template's project structure, build commands, conventions, and any template-specific agent guidance. Wrap the new content in `<!-- BEGIN TEMPLATE -->` and `<!-- END TEMPLATE -->` markers — these tell end-users' `/setup-repo` agent which content to replace with project-specific context.
