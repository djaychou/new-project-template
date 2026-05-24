# Security Audit Remediation Workflow

Reference for `/execute-plan` when working on security audit findings.

## Source Files

### Master Tracker
- **Path**: `docs/security-audits/master.md`
- **Contains**: Audit run history, remediation status table (all findings across runs), remaining tasks, needs-verification items, remediation doc links
- **Key table**: `## Current Remediation Status` — columns: ID, Severity, Title, Status, Fixed In, Notes

### Per-Run Artifacts (in `docs/security-audits/{date}/`)

| File | Purpose | When to Read |
|---|---|---|
| `SECURITY_AUDIT_REPORT.md` | Final consolidated report with all findings, severity, exploit scenarios, fix recommendations | Always — primary source of finding details |
| `SECURITY_REMEDIATION.md` | Remediation log — what was fixed, how, which files changed | Read if exists — check what's already been done |
| `SECURITY_RECON.md` | Architecture, stack, trust boundaries, attack surface | Read when you need broader context about the system |
| `SECURITY_AUDIT_PLAN.md` | Which passes were planned and why | Rarely needed during remediation |
| `SECURITY_FINDINGS_MAIN.md` | Detailed main review findings | Read for deeper context on specific findings |
| `SECURITY_FINDINGS_SPECIALIST_*.md` | Specialist pass findings (static-analysis, insecure-defaults, sharp-edges, supply-chain, ci-automation) | Read for deeper context on specialist findings |

## Finding Structure

Each finding in `SECURITY_AUDIT_REPORT.md` has:
```
### [REPORT-XXX] Title
- severity: critical | high | medium | low
- status: new | still-present
- source_pass: which agent found it
- original_id: ID from the source pass (e.g., MAIN-001, SA-001)
- files: [file:line references]
- summary: one-line description
- exploit_scenario: how it can be exploited
- fix: specific remediation recommendation
- fix_type: quick-win | structural | external-dependency
```

## Phasing Strategy

Default grouping by severity:
1. **Phase 1**: Critical + High — fix immediately
2. **Phase 2**: Medium quick-wins (fix_type: quick-win)
3. **Phase 3**: Medium structural (fix_type: structural)
4. **Phase 4**: Low severity

Within each phase, group items that touch the same files together for efficient implementation.

## Documentation Updates

After completing items, update two files:

### 1. `docs/security-audits/{date}/SECURITY_REMEDIATION.md`

If it doesn't exist, create it with this structure:
```markdown
# Security Audit Remediation — {date}

**Created:** {today's date}
**Audit report:** `SECURITY_AUDIT_REPORT.md`

## Summary
{Brief: how many findings fixed, which phases}

## Phase N — {Phase Name}

### REPORT-XXX: {Title} ({Severity})
**Problem:** {one sentence}
**Fix:** {what was done}
**Files changed:**
- `path/to/file.ts:lines` — {what changed}
```

If it exists, append new phases/findings. Add an Edit History entry.

### 2. `docs/security-audits/master.md`

- Change finding status from `**Open**` to `**Fixed**`
- Add `Fixed In` value (e.g., "Phase 3")
- Add brief `Notes` (1 sentence: what was done)
- Update `## Remaining Tasks` — remove completed items
- Update `Last Updated` date

## Validation

For this project (Next.js + TypeScript): run `npx tsc --noEmit` after each phase.
