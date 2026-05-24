---
name: seo-schema
description: >
  Detect, validate, and generate Schema.org structured data. JSON-LD format
  preferred. Used by the seo-optimizer agent for parallel analysis.
user-invokable: false
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
  - WebFetch
  - Write
---

# Schema Markup Analysis & Generation

Load and follow the workflow in `.claude/skills/seo/references/schema-workflow.md`.

For schema types and deprecation status, load `.claude/skills/seo/references/schema-types.md`.

For JSON-LD templates, load `.claude/skills/seo/schema/templates.json`.
