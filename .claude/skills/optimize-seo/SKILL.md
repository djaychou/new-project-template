---
name: optimize-seo
description: >
  Autonomous SEO optimization workflow for Next.js App Router projects.
  Delegates to the seo-optimizer agent which audits, scores, presents findings,
  implements fixes, and re-evaluates. Generates docs/seo-audit-{date}.md.
  Triggers on: "/optimize-seo", "optimize seo", "run seo audit", "seo optimization".
user-invokable: true
argument-hint: "[optional: focus-area or localhost-url]"
---

# /optimize-seo

When invoked, delegate to the **seo-optimizer** agent.

The agent runs a 5-phase workflow:
1. **Setup** — checks if `.claude/agents/seo-optimizer.md` and skill files exist; creates them if missing
2. **Audit** — manual code review (15 grep patterns) + claude-seo skills + Lighthouse
3. **Present** — scores 78 eval items, shows health score + action items, waits for approval
4. **Implement** — applies approved fixes, verifies build
5. **Re-evaluate** — verifies fixes, recalculates before/after score

## Self-Setup (new projects)

If `.claude/agents/seo-optimizer.md` does not exist, read
`.claude/skills/optimize-seo/references/setup-templates.md` and create:

1. `.claude/agents/seo-optimizer.md` — the SEO optimizer agent
2. `.claude/skills/optimize-seo/evals-checklist.md` — 78-item eval masterlist
3. `.claude/skills/optimize-seo/audit-template.md` — audit output skeleton

Do NOT create a `docs/SEO Optimizer/` directory. The only output in `docs/`
is the audit file: `docs/seo-audit-{YYYY-MM-DD}.md`.

## Reference Files (in `references/`)

| File | Purpose |
|------|---------|
| `references/evals-checklist.md` | 78 eval items across 11 weighted categories |
| `references/audit-template.md` | Skeleton for the audit output document |
| `references/setup-templates.md` | Content templates for self-setup in new projects |

## Focus Areas

Pass a focus area to narrow the audit scope:

| Argument | Scope |
|----------|-------|
| `technical` | Crawlability, Semantics, Security |
| `content` | Content Quality, Internal Linking |
| `schema` | Structured Data |
| `performance` | Performance, Images |
| `images` | Images only |
| `geo` / `ai` | AI Search Readiness |
| `full` (default) | All 11 categories |
