---
name: seo-images
description: >
  Image optimization analysis for SEO and performance. Checks alt text, file
  sizes, formats, responsive images, lazy loading, and CLS prevention.
  Used by the seo-optimizer agent for parallel analysis.
user-invokable: false
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
  - WebFetch
---

# Image Optimization Analysis

Load and follow the workflow in `.claude/skills/seo/references/images.md`.

For quality gates on image file sizes, load `.claude/skills/seo/references/quality-gates.md`.
