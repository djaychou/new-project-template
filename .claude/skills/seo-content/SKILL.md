---
name: seo-content
description: >
  Content quality and E-E-A-T analysis with AI citation readiness assessment.
  Used by the seo-optimizer agent for parallel analysis.
user-invokable: false
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
  - WebFetch
---

# Content Quality & E-E-A-T Analysis

Load and follow the workflow in `.claude/skills/seo/references/content.md`.

For E-E-A-T evaluation criteria, load `.claude/skills/seo/references/eeat-framework.md`.

For quality gates (word count thresholds, uniqueness), load `.claude/skills/seo/references/quality-gates.md`.
