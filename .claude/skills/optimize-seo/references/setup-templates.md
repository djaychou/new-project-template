# Setup Templates — /optimize-seo

> Loaded ONLY during Phase 0 (self-setup) when the seo-optimizer agent or
> skill files don't exist in the project. Contains instructions for creating
> all required files. After setup, this file is never loaded again.

## Instructions

When Phase 0 triggers, create the following files. Skip any that already exist.

### Step 1: Create directories

```
mkdir -p .claude/agents
mkdir -p .claude/skills/optimize-seo
```

### Step 2: Create the SEO optimizer agent

Write `.claude/agents/seo-optimizer.md` with:
- YAML frontmatter: name, description, tools (Read, Grep, Glob, Bash, Write, Edit, Agent, Skill, WebFetch), model: inherit, memory: project
- Skills field: seo, seo-technical, seo-content, seo-schema, seo-images, seo-geo
- System prompt containing the full 5-phase workflow:
  - Phase 0: Self-setup (check files exist, create if missing)
  - Phase 1: Audit (codebase discovery, 15 manual grep patterns, invoke claude-seo skills, Lighthouse, score 78 eval items, generate audit doc, regression check)
  - Phase 2: Present (health score table, evals summary, priority action items, STOP and wait for approval)
  - Phase 3: Implement (queue by severity, implement each item, verify, build check, summarize)
  - Phase 4: Re-evaluate (verify DONE items, regression check, recalculate scores, update doc)
  - Error handling table
  - Focus areas table
  - Memory instructions for persistent learning

Use the existing `.claude/agents/seo-optimizer.md` from any project that has it as reference. The agent's system prompt should be self-contained — it IS the full workflow, not a pointer to external files.

### Step 3: Create the skill trigger

Write `.claude/skills/optimize-seo/SKILL.md` with:
- YAML frontmatter: name: optimize-seo, user-invokable: true, argument-hint
- Body: "Delegate to the seo-optimizer agent"
- Self-setup instructions (what to create if missing)
- Reference files table
- Focus areas table

### Step 4: Create the evals checklist

Write `.claude/skills/optimize-seo/evals-checklist.md` with 78 eval items across 11 categories:

| Category | Weight | Items |
|----------|--------|-------|
| 1. Crawlability & Indexing | 12% | 12 items (C-01 to C-12) |
| 2. HTML Semantics | 8% | 6 items (H-01 to H-06) |
| 3. Metadata | 10% | 8 items (M-01 to M-08) |
| 4. Structured Data | 10% | 8 items (S-01 to S-08) |
| 5. Performance & CWV | 10% | 9 items (P-01 to P-09) |
| 6. Security Headers | 5% | 5 items (X-01 to X-05) |
| 7. Content Quality & E-E-A-T | 23% | 11 items (Q-01 to Q-11) |
| 8. Internal Linking | 8% | 6 items (L-01 to L-06) |
| 9. AI Search Readiness / GEO | 10% | 8 items (G-01 to G-08) |
| 10. Images | 5% | 7 items (I-01 to I-07) |
| 11. Lighthouse Validation | derived | 8 items (LH-01 to LH-08) |

Each item needs: unique ID, check description, source attribution, point weight.
Scoring: PASS = full points, WARN = half, FAIL = 0, N-A = excluded.

### Step 5: Create the audit template

Write `.claude/skills/optimize-seo/audit-template.md` with sections:
1. Audit Methodology
2. Summary (placeholder)
3. SEO Health Score table (10 categories with weight/score/weighted columns)
4. Evals Checklist (all 11 categories with ID/Check/Status/Score/Notes tables)
5. Issues Found (CRITICAL/HIGH/MEDIUM/LOW)
6. Lighthouse Results (page/device/score table)
7. Priority Action Items (with Status: PENDING/APPROVED/DONE/DEFERRED/REJECTED/FAILED)
8. Deferred Items table
9. Rejected Items table
10. Project-Specific Context (empty, accumulates over time)
11. Changelog

### Step 6: Copy this file

Write `.claude/skills/optimize-seo/setup-templates.md` — copy this file so the
setup can be repeated in projects that receive the skill but not this file.

### Step 7: Report

Tell the user:
"SEO Optimizer initialized. Created agent at `.claude/agents/seo-optimizer.md`
and skill files at `.claude/skills/optimize-seo/`. Run `/optimize-seo` to start."

## What NOT to create

- Do NOT create `docs/SEO Optimizer/` or any subdirectories
- Do NOT create `docs/SEO Optimizer/agents/`, `docs/SEO Optimizer/evals/`, etc.
- The only file created in `docs/` is the per-audit output: `docs/seo-audit-{YYYY-MM-DD}.md`
- Do NOT create a master-context.md — that's optional project documentation, not required for the agent to function
