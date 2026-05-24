# SEO Audit Remediation Workflow

Reference for `/execute-plan` when working on SEO audit findings.

## Source Files

### Audit Reports
- **Path pattern**: `docs/seo-audit-{date}.md`
- **Discovery**: Glob for `docs/seo-audit-*.md`, use most recent by date
- **Structure**: Single file containing checklist, issues, and notes

## Issue Structure

SEO audit files organize issues under `## Issues Found`, grouped by severity:

```markdown
## Issues Found

### HIGH Severity

#### 1. {Issue title}
{Description of the problem and why it matters for SEO}

**Files:** or **File:**
- `path/to/file.tsx` line N: {what's wrong}

**Fix:** {Specific remediation steps}

---

### MEDIUM Severity
...

### LOW Severity
...
```

Each issue has:
- A numbered title
- Severity level (HIGH, MEDIUM, LOW) from the parent heading
- Description with SEO impact explanation
- File references with line numbers
- Specific fix instructions

## Phasing Strategy

Default grouping by severity:
1. **Phase 1**: HIGH severity
2. **Phase 2**: MEDIUM severity
3. **Phase 3**: LOW severity

Within a phase, group issues that touch the same component or file.

## Documentation Updates

SEO audits use inline documentation — update the source audit file directly.

### Marking Issues as Fixed

Add a status line after the issue title and before the description:

```markdown
#### 1. Multiple `<h1>` tags per page
**Status: Fixed** (2026-03-23) — Changed Header and Footer `<h1>` to `<span>`

{original description unchanged}
```

### Checklist Updates

If the audit has a checklist section (`- [x]` / `- [ ]` items), check off items that are resolved by the fixes.

### Summary Update

If the audit has a `## Summary` section, append a remediation note:

```markdown
**Remediation update ({date}):** {N} of {total} issues fixed. Remaining: {list remaining issue numbers}.
```

## Validation

For this project (Next.js + TypeScript): run `npx tsc --noEmit` after each phase.

For SEO-specific validation, suggest the user run a Lighthouse audit on affected pages after implementation.
