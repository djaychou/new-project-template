# Doc Templates

All templates follow a consistent structure. Adapt as needed but preserve required sections.

---

## Workflow Template

```markdown
# {Workflow Name}

**Created:** {YYYY-MM-DD}
**Summary:** {1-2 sentence overview of this workflow}

## Trigger
{What initiates this workflow — user action, system event, cron, etc.}

## Steps

1. **{Actor}:** {Step description}
   - Input: {data/format}
   - Output: {data/format}
   - Code: `{file_path}:{line}`

2. **{Actor}:** {Step description}
   ...

## Data Flow
{Describe data transformations between steps. Use a simple diagram if helpful:}
```
Input → Step 1 → Step 2 → Output
```

## Error Paths
| Step | Failure | Handling |
|---|---|---|
| {step} | {what fails} | {what happens} |

## Key Files
- `{file_path}` — {purpose}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Feature Template

```markdown
# {Feature Name}

**Created:** {YYYY-MM-DD}
**Summary:** {1-2 sentence overview}

## Context
{Why this feature exists. What problem it solves.}

## Workflow Integration
{Where in the user journey this feature appears. Reference workflow docs if they exist.}

## Implementation
| Component | File | Purpose |
|---|---|---|
| {name} | `{file_path}:{line}` | {what it does} |

## Purpose and Outcome
{What the user gains from this feature.}

## Constraints
- {Limits, edge cases, known issues}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Audit Template

```markdown
# {Audit Type} Audit — {Scope}

**Created:** {YYYY-MM-DD}
**Audit Type:** {security | seo | code | performance}
**Summary:** {1-2 sentence overview of findings}

## Scope
{What was audited — files, endpoints, pages, etc.}

## Findings

### Critical
- {Finding with code reference if applicable}

### Warning
- {Finding}

### Info
- {Finding}

## Recommendations
| Priority | Finding | Fix |
|---|---|---|
| High | {issue} | {action} |
| Medium | {issue} | {action} |
| Low | {issue} | {action} |

## Results
{Metrics, scores, pass/fail counts, before/after if applicable}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Config Template

```markdown
# {Service/Tool} Configuration

**Created:** {YYYY-MM-DD}
**Summary:** {1-2 sentence overview}

## Service
{What this config is for, why it's needed}

## Variables
| Variable | Purpose | Format | Default | Required |
|---|---|---|---|---|
| `{VAR_NAME}` | {purpose} | {string/url/key} | {default or N/A} | {yes/no} |

## Dependencies
{What breaks if misconfigured. Which services/features rely on these configs.}

## Environment Differences
| Variable | Dev | Prod |
|---|---|---|
| `{VAR}` | {dev value/behavior} | {prod value/behavior} |

## Key Files
- `{file_path}` — {where config is loaded/used}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Decision Template

```markdown
# Decision: {Title}

**Created:** {YYYY-MM-DD}
**Status:** {active | superseded | deprecated}
**Summary:** {1-2 sentence overview of the decision}

## Context
{What situation prompted this decision. What constraints exist.}

## Options Considered
1. **{Option A}** — {pros/cons in 1 line}
2. **{Option B}** — {pros/cons in 1 line}

## Decision
{What was chosen and why.}

## Consequences
- **Enables:** {what this makes possible}
- **Prevents/limits:** {tradeoffs accepted}
- **Requires:** {any follow-up work or maintenance}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Integration Template

```markdown
# Integration: {Service Name}

**Created:** {YYYY-MM-DD}
**Summary:** {1-2 sentence overview}

## Service
{Name, purpose, why chosen over alternatives}

## Connection
- **Auth method:** {API key / OAuth / WebSocket / etc.}
- **Base URL:** {endpoint if applicable}
- **Config:** `{env var or config file}`

## Key Files
| File | Purpose |
|---|---|
| `{file_path}` | {what it does} |

## Rate Limits / Quotas
| Limit | Value | Impact |
|---|---|---|
| {limit type} | {value} | {what happens when exceeded} |

## Gotchas
- {Non-obvious behavior or known issue}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```

---

## Database Template

```markdown
# {Table/Entity Name}

**Created:** {YYYY-MM-DD}
**Summary:** {1-2 sentence overview}

## Schema
| Column | Type | Constraints | Purpose |
|---|---|---|---|
| `{column}` | {type} | {PK/FK/NOT NULL/DEFAULT/etc.} | {purpose} |

## Relationships
- `{column}` → `{other_table.column}` ({relationship type})

## RLS Policies
| Policy | Operation | Rule |
|---|---|---|
| {name} | {SELECT/INSERT/UPDATE/DELETE} | {condition} |

## Indexes
| Index | Columns | Rationale |
|---|---|---|
| `{index_name}` | `({columns})` | {why this index exists} |

## Migration Notes
{Changes from original schema, version history}

## Edit History
- {YYYY-MM-DD}: Initial creation

## Archive
{Empty until content becomes obsolete}
```
