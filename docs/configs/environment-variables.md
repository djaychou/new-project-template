# Environment Variables Configuration

**Created:** 2026-10-07
**Summary:** Every environment variable and secret the project reads. Add a row whenever code starts reading a new one.

## Service
All services. Secrets never go in a variable the client bundle can read (`VITE_*`, `NEXT_PUBLIC_*`); see CLAUDE.md rule 12.

## Variables
| Variable | Purpose | Format | Default | Required | Client-visible |
|---|---|---|---|---|---|

## Dependencies
{What breaks if misconfigured. Which services/features rely on these configs.}

## Environment Differences
| Variable | Dev | Prod |
|---|---|---|

## Key Files
- {file_path} — {where config is loaded/used}

## Edit History
- 2026-10-07: Initial creation

## Archive
{Empty until content becomes obsolete}
