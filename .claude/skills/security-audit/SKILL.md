---
name: security-audit
description: >
  Run a full security audit or incremental security audit on any codebase.
  Detects first-run vs incremental mode automatically. Orchestrates recon,
  planning, main review, specialist passes, and final report generation.
  All artifacts stored in docs/security-audits/{date}/. Stack-agnostic —
  discovers everything dynamically. Triggers on: "/security-audit",
  "security audit", "run security audit".
user-invokable: true
argument-hint: "[optional: focus-area or scope override]"
---

# /security-audit

When invoked, detect audit mode and delegate to the **security-audit-orchestrator** agent.

## Mode Detection

1. Check if `docs/security-audits/` contains any previous audit folders (date-named directories with `SECURITY_AUDIT_REPORT.md` inside).
2. If **no previous audit exists** → mode is `fresh`.
3. If **previous audit exists** → mode is `incremental`. Identify the most recent date folder as `previous_run`.

## Config Detection

1. Check if `docs/security-audits/audit-config.yml` exists.
2. If it exists, read it and pass contents to the orchestrator.
3. If it does not exist, proceed with defaults (no custom passes, no custom rules, no skipped passes).

## Context Gathering

1. Read `CLAUDE.md` at project root (if exists) for project context.
2. Read any `.claude/CLAUDE.md` or `.claude/instructions.md` for additional context.
3. Determine today's date for the output folder name.

## Delegation

Launch the `security-audit-orchestrator` agent with:

```
Mode: {fresh | incremental}
Date: {YYYY-MM-DD}
Previous run: {path to previous date folder, or null}
Config: {contents of audit-config.yml, or "defaults"}
Project context: {summary from CLAUDE.md}
Focus area: {user-provided argument, or "full"}
```

## Self-Setup (new projects)

If `.claude/agents/security-audit-orchestrator.md` does not exist, read
`.claude/skills/security-audit/references/setup-templates.md` and create all required
agent files before proceeding.

## Prerequisite Check (MUST run before delegation)

The audit depends on global Claude Code plugins (Sentry + Trail of Bits). These are NOT local project files — they're installed once via `/plugin` and available across all projects. They show up in the `<system-reminder>` skills list that Claude Code injects into every conversation.

**Check**: scan the available skills list in your current conversation context for these names.

**Required global plugins:**
- `security-review` (Sentry)
- `find-bugs` (Sentry)
- `audit-context-building` (Trail of Bits)
- `static-analysis` (Trail of Bits)
- `insecure-defaults` (Trail of Bits)
- `supply-chain-risk-auditor` (Trail of Bits)
- `sharp-edges` (Trail of Bits)
- `differential-review` (Trail of Bits)

**Optional (note absence but don't block):**
- `agentic-actions-auditor` (Trail of Bits)

**If any required plugin is NOT in the available skills list, STOP and output:**

```
## Security Audit — Missing Prerequisites

The following required global plugins are not installed:

{list each missing item}

These are global Claude Code plugins, not project files. They only need to be installed once and work across all projects.

### Install Instructions

**Sentry skills:**
git clone https://github.com/getsentry/skills.git ~/sentry-skills
claude plugin marketplace add ~/sentry-skills
claude plugin install sentry-skills

**Trail of Bits plugins (run inside Claude Code):**
/plugin marketplace add trailofbits/skills
/plugin menu
→ Install: audit-context-building, static-analysis, insecure-defaults,
  supply-chain-risk-auditor, sharp-edges, differential-review

Optional:
→ Install: agentic-actions-auditor

After installing, run /security-audit again.
```

**If all required plugins are present, proceed to Delegation.**
