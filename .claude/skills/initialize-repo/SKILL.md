---
name: initialize-repo
description: >-
  Transforms this template repo into a real project. Invoke after creating a repo
  from this template. Replaces placeholder names, configures Beads issue tracking,
  and verifies the project builds. Self-destructs after completion.
---

Initialize Repository
=====================

This skill transforms the template into a working project.

Read `SETUP_CHECKLIST.md` and execute each item in order, checking them off as you go. The checklist is the single source of truth for all initialization steps.

For items that require user input (GitHub owner, project name, license choice), ask the user and wait for their response before proceeding.

For items marked with `🚨 USER ACTION`, surface them clearly and wait for the user to confirm completion before continuing.
