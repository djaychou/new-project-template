---
name: execute-plan
description: >
  Implements action items from structured trackers (master.md files) or markdown
  checklists. Reads the source, presents items for selection, plans implementation
  in phases, executes with validation, and documents results. Handles security audit
  remediation, SEO audit fixes, and any markdown-based tasklist. Triggers on:
  "/execute-plan", "execute plan", "fix remaining tasks", "implement audit findings",
  "work through the tasklist".
user-invokable: true
argument-hint: "[path to tracker/checklist file, or 'security' / 'seo' for known audit types]"
metadata:
  author: Djay
  version: 1.0.0
---

# /execute-plan

Implements action items from structured trackers or markdown checklists through a guided workflow: select items, confirm plan, implement in phases, validate, test, and document.

## Critical Rules

1. **Never implement without user confirmation.** Always present the plan and wait for approval.
2. **Research happens after selection, not before.** Don't read the entire codebase upfront.
3. **Document only completed items.** If a session ends early, only update docs for what was actually fixed.
4. **Ask documentation target before implementation.** Confirm where results will be documented before writing any code.
5. **Run project-appropriate validation after each phase.** Detect from project context (e.g., `tsc --noEmit` for TypeScript, `npm run lint` for JS, `python -m pytest` for Python). Check `package.json`, `Makefile`, `pyproject.toml`, or equivalent to determine the right command.
6. **Follow documenter conventions** when writing or updating documentation — created date, edit history, structured format, concise and AI-readable.

## Input Modes

### Mode 1: Structured Tracker (master.md)

For audit trackers with tables containing status columns (Open, Fixed, etc.):
1. Read the tracker file
2. Extract open/pending items
3. Group by audit run, severity, or category (depending on tracker structure)
4. Present groups to user for selection

**Detection:** File contains markdown tables with a status-like column (Status, Fixed In, etc.) and items marked as Open, Pending, or equivalent.

### Mode 2: Markdown Checklist

For any markdown file with actionable items:
1. Read the file
2. Extract unchecked items (`- [ ]`), numbered lists, or table rows marked as incomplete
3. Present items to user for selection

**Detection:** File contains `- [ ]` checkboxes, numbered action items, or tables with issue/fix columns.

### Shortcut Arguments

- `security` → reads `docs/security-audits/master.md`
- `seo` → reads the most recent `docs/seo-audit-*.md`
- A file path → reads that file directly

If no argument provided, ask the user what to work on.

## Workflow

### Step 1: Context

Read the source file. Present open items to the user.

- **Structured trackers**: Group items by run/category. Show: ID, severity/priority, title, current status.
- **Markdown checklists**: Show items as a numbered list with any available context (severity, file references).

If items span multiple audit runs or categories, show them grouped. Default selection: most recent group. Allow cross-group selection with a note that documentation will update multiple files.

### Step 2: Select

Ask the user which items to work on. Accept:
- "All" — everything open
- Specific IDs or numbers — "1, 3, 5" or "REPORT-006, REPORT-013"
- A group — "all medium severity", "Phase 3 items"

### Step 3: Documentation Target

Before any implementation, confirm where to document results.

**For known audit types** (see Type-Specific Workflows below): state the default targets and confirm with user.

**For generic tasklists**: ask the user:
> "Where should I document the completed work? Options: update the source file directly, create/update a doc in `docs/`, or skip documentation."

If the user provides a location, check if files exist there. If not, they will be created after implementation.

### Step 4: Phase

If 4+ items selected, auto-group into phases:
- **Severity-based** (default for audits): critical+high → medium → low
- **File-based**: group items that touch the same files
- **Logical**: group by feature area or dependency

Present the proposed phases. User can:
- Accept as-is
- Request different grouping
- Flatten to a single tasklist
- Reorder phases

**Gate: Wait for user confirmation before proceeding.**

### Step 5: Research + Plan

For each phase (or the single tasklist):
1. Read the referenced files from the source document
2. Read surrounding code for context
3. Check current state — some items may already be partially addressed
4. Build a concrete action plan: what changes, in which files, and why

Present the action plan to the user. For each item, show:
- What will change
- Which files will be modified/created
- The approach

**Gate: Wait for user confirmation. Adapt to any requested changes.**

### Step 6: Implement

Execute the plan. Track progress per item using todos.

- **Phased**: Complete one phase, run validation, then ask user approval before next phase
- **Single tasklist**: Complete all items, run validation once

**Validation**: Detect the appropriate check from project context:
- Check `package.json` scripts, `Makefile`, `pyproject.toml`, or equivalent
- TypeScript project → `npx tsc --noEmit`
- Node.js project → `npm run lint` (if available)
- Python project → type checker / linter as configured

If validation fails, fix issues before proceeding.

### Step 7: Test

Categorize each completed item:

- **Testable** (API endpoints, forms, UI changes): Present specific manual test steps. **Gate: Wait for user to confirm tests pass.**
- **Observable** (headers, config, escaping, server-side logic): Note what changed and what to observe. No gate — proceed to documentation.

If a phase has both testable and observable items, only gate on the testable ones.

### Step 8: Document

Update documentation for completed items only.

1. **Update source tracker/checklist**: Mark items as fixed/done. Add brief context per item (1 sentence: what was done).
2. **Update documentation target** (confirmed in Step 3): Add or update entries for each fixed item. Follow documenter conventions — structured format, code references, edit history if updating existing doc.
3. Update `Last Updated` date on any modified tracker.

If the session ended early (not all items completed), document only what was completed. Leave remaining items as open.

## Proactive Suggestion

When a user provides inline action items or a checklist in chat (3+ tasks, numbered lists, or bullet points describing changes to implement), suggest using this skill:

> "This looks like a structured tasklist. Want to run `/execute-plan` to track, implement, and document these systematically?"

Do not auto-trigger. Let the user decide. If they decline, proceed normally without the skill workflow.

## Type-Specific Workflows

For recurring audit types, these sections define source locations and documentation targets. Consult `references/` for detailed workflow docs.

### Security Audits

**Source**: `docs/security-audits/master.md` or a specific run folder `docs/security-audits/{date}/`

**Documentation targets** (confirm with user, these are defaults):
- `docs/security-audits/{date}/SECURITY_REMEDIATION.md` — detailed per-finding remediation notes
- `docs/security-audits/master.md` — update status table and remaining tasks

**Phasing default**: By severity (critical+high → medium → low)

**Context files**: When researching a finding, read both the `SECURITY_AUDIT_REPORT.md` and the relevant `SECURITY_FINDINGS_*.md` for full context on the finding (exploit scenario, fix recommendation, referenced files).

**See**: `references/security-audit-workflow.md`

### SEO Audits

**Source**: Most recent `docs/seo-audit-*.md`

**Documentation targets** (confirm with user, these are defaults):
- The source audit file itself — update issue status inline

**Phasing default**: By severity (HIGH → MEDIUM → LOW)

**Context**: SEO audit files contain file references and specific fix instructions per issue. Read the full issue description before researching.

**See**: `references/seo-audit-workflow.md`
