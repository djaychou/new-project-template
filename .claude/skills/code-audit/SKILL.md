---
name: code-audit
description: >
  Run a code audit to find duplicate/redundant implementations and dead/unused code.
  Detects first-run vs incremental mode automatically. Orchestrates inventory building,
  consolidation finding, dead code detection, and UI deduplication.
  All artifacts stored in docs/code-audits/. Stack-agnostic — discovers everything
  dynamically. Triggers on: "/code-audit", "code audit", "run code audit",
  "find duplicates", "find dead code", "clean up codebase".
user-invokable: true
argument-hint: "[optional: fresh | focus:frontend | focus:backend | focus:ui]"
---

# /code-audit

When invoked, detect audit mode and delegate to the **code-audit-orchestrator** agent.

## Argument Parsing

Parse the optional user argument:

- **No argument** → full audit, auto mode
- `fresh` → force full rescan, ignore previous reports
- `focus:frontend` → only run consolidation-finder + ui-deduplication on frontend source
- `focus:backend` → only run consolidation-finder + dead-code-finder on backend source
- `focus:ui` → only run ui-deduplication
- Any combination like `fresh focus:frontend` is valid

## Mode Detection

1. List directories inside `docs/code-audits/` to find previous date-stamped audit folders (YYYY-MM-DD format).
2. If **no previous folders exist** → mode is `fresh` (first run).
3. If **previous folders exist** → mode is `auto` (agents will auto-detect incremental). Identify the most recent date folder as `previous_run`.
4. If user passed `fresh` → override to `fresh` regardless.

## Context Gathering

1. Read `CLAUDE.md` at project root (if exists) for project context.
2. Determine today's date (YYYY-MM-DD) for the output folder name.
3. Determine the output directory: `docs/code-audits/{YYYY-MM-DD}/`
4. Create the output directory if it doesn't exist.
5. If `previous_run` exists, pass its path so agents can read previous reports for incremental mode.

## Prerequisite Check

Verify the required agent files exist in `.claude/agents/`:

- `inventory-builder.md`
- `consolidation-finder.md`
- `dead-code-finder.md`
- `ui-deduplication.md`
- `code-audit-orchestrator.md`

**If any agent file is missing, STOP and output:**

```
## Code Audit — Missing Agent Files

The following required agent definitions are not found:

{list each missing file}

These should be in `.claude/agents/`. They may have been deleted or not set up.
Please restore them before running /code-audit again.
```

## Delegation

Launch the `code-audit-orchestrator` agent with:

```
output_dir: docs/code-audits/{YYYY-MM-DD}/
previous_run: {path to most recent previous date folder, or null}
mode: {fresh | auto}
project_context: {summary from CLAUDE.md, or "none — agents will discover"}
focus: {full | frontend | backend | ui}
```

## Focus Mode Behavior

When a focus is specified, tell the orchestrator to:

- `focus:frontend` → inventory-builder scans only frontend dirs, skip backend. Run consolidation-finder + ui-deduplication. Skip dead-code-finder's backend function tracing.
- `focus:backend` → inventory-builder scans only backend dirs. Run consolidation-finder + dead-code-finder. Skip ui-deduplication.
- `focus:ui` → inventory-builder scans only component dirs. Run only ui-deduplication.
- `full` (default) → run everything.

## Post-Run

After the orchestrator completes, output a brief summary to the user:

```
## Code Audit Complete

Mode: {fresh | incremental}
Reports saved to: docs/code-audits/{YYYY-MM-DD}/

| Finding | Count |
|---------|-------|
| Duplicate groups | {n} |
| Dead code items | {n} |
| UI pattern groups | {n} |
| Top priority actions | {n} |

Review the full report: docs/code-audits/{YYYY-MM-DD}/AUDIT-SUMMARY.md
```
