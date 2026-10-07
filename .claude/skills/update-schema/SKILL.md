---
name: update-schema
description: >
  Refreshes database/schema.sql to match the current production Supabase
  schema. In repos with .github/workflows/sync-schema.yml, CI is the single
  writer: this skill triggers CI, then copies the dump from the `schema` branch onto disk. In repos without it, dumps
  locally via SUPABASE_DB_URL. Triggers when user says "update schema",
  "sync schema", "refresh schema", or "dump schema".
user-invokable: true
---

# Update Schema

## Single-writer rule — read this first

**If `.github/workflows/sync-schema.yml` exists in the repo, CI owns `database/schema.sql`** — it lives on the
`schema` branch and is gitignored on main.
**Do not dump it locally. Use Path A.**

### Why (do not undo this without reading)

`schema.sql` is produced by `pg_dump`. Two different `pg_dump` major versions render the
*same* permissions with *different text*:

| pg_dump | Renders table grants as |
|---|---|
| 15.x | `GRANT ALL ON TABLE "public"."x" TO "anon";` |
| 17.x | `GRANT SELECT,INSERT,REFERENCES,DELETE,TRIGGER,TRUNCATE,UPDATE ON TABLE "public"."x" TO "anon";` |

PostgreSQL 17 added the `MAINTAIN` privilege, so "ALL" went from 7 privileges to 8. A database
granting the 7 classic privileges is "everything" to pg_dump 15 (prints `GRANT ALL`) but
"7 of 8" to pg_dump 17 (must spell them out). **Both forms mean exactly the same thing.**

CI and a developer laptop routinely run different `pg_dump` majors even on the *same* pinned
Supabase CLI version: the pin covers the CLI, **not** the `pg_dump` underneath it. Measured on
2026-09-14, CI ran the *newer* pg_dump and the laptop the older one.

Whether that difference is visible depends on the **server's** privilege set, which is why some
databases churn and others never do:

| Grants on the server | pg_dump 17 prints | pg_dump <=16 prints | Agree? |
|---|---|---|---|
| 8 of 8, incl. `MAINTAIN` (PG17-era server) | `GRANT ALL` | `GRANT ALL` | yes |
| the 7 classic privileges (older server) | expanded list | `GRANT ALL` | **no** |

So a repo can look immune for months and start churning the moment grants are re-applied, or the
server is upgraded. Do not conclude from a quiet history that local dumping is safe here.

Consequence when both sides write the file: every local dump flips ~50 lines one way, the next CI
run flips them back, and each round trip produces a large conflict in which **no schema actually
changed**. On 2026-09-14 this caused a rejected push that looked like lost work but was purely
cosmetic. One writer removes the problem by construction.

---

## Path A — repo HAS `.github/workflows/sync-schema.yml` (default)

CI already syncs on a schedule (see the `cron` line in that workflow). Use this path only when the
user wants an **immediate** refresh after a database change.

### Step A1 — confirm CI owns the file

```bash
ls .github/workflows/sync-schema.yml
```

If it exists, continue here. If not, go to Path B.

### Step A2 — trigger the sync and wait

```bash
gh workflow run sync-schema.yml
gh run watch "$(gh run list --workflow=sync-schema.yml --limit 1 --json databaseId -q '.[0].databaseId')" --exit-status
```

If `gh run list` returns nothing on the first try the run has not registered yet — re-run that
command once before treating it as a failure.

If `gh` is not installed or not authenticated, tell the user to run `gh auth login`, or to trigger
the workflow from the GitHub Actions tab. **Do not fall back to dumping locally** — that is the
behaviour this rule exists to prevent.

### Step A3 — copy the result onto disk

CI publishes to the orphan `schema` branch, not to main. `database/schema*.sql` is
gitignored on main, so do **not** `git pull` for this — it would fetch nothing
relevant. Copy the files down instead, bypassing the hook's throttle:

```bash
.claude/hooks/refresh-schema.sh --force
```

It prints which files it refreshed, or that the schema was already current.

**Repos with generated types** (`TYPES_FILE:` is non-empty in the workflow — today only
speakship-admin): the same run may also have committed regenerated types to **main**.
Pull those:

```bash
grep -q '^  TYPES_FILE: *[^ ]' .github/workflows/sync-schema.yml && git pull
```

If that run *failed* at "Commit generated types", the schema moved and app code has
not caught up — the types were withheld on purpose. See docs/schema-sync.md.

### Step A4 — report

Show what changed in the database since the previous dump:

```bash
git log -2 --format='%h %cd' --date=format:'%Y-%m-%d %H:%M' origin/schema
git diff origin/schema~1 origin/schema --stat
git diff origin/schema~1 origin/schema -- database/ | grep '^[+-]' | grep -v '^[+-][+-]' | grep -v GRANT
```

Summarise for the user: new tables, new or removed columns, new indexes or policies.
If CI reported "Schema unchanged", say the schema was already current.

---

## Path B — repo has NO sync-schema.yml (local dump)

Only for repos without the CI workflow.

### Step B1 — check the CLI

```bash
supabase --version
```

If missing, tell the user to install it (`brew install supabase/tap/supabase` on macOS, or `npx supabase`).

### Step B2 — find the connection string

`SUPABASE_DB_URL` may live in `.env.local` rather than `.env`:

```bash
grep -l "SUPABASE_DB_URL" .env .env.local 2>/dev/null
```

Use whichever file the grep reports; do not assume `.env`.

If unset, ask the user to add it to `.env.local` (env files are gitignored). Copy it from the
Supabase dashboard: **Project → Connect → Session pooler**. Format:

```
SUPABASE_DB_URL=postgresql://postgres.<project-ref>:<password>@aws-0-<region>.pooler.supabase.com:5432/postgres
```

Percent-encode special characters in the password (`@` → `%40`, `#` → `%23`).

Why a connection string instead of `supabase link`: dumping needs only *database* access, not
account API access. Linking depends on which account the CLI is logged into, which silently breaks
when a token expires or the user has multiple accounts.

### Step B3 — dump

**Never pass `--schema`.** It makes the CLI hand `pg_dump --schema=<list>`, which overrides its
default `--exclude-schema` mode and silently drops `CREATE EXTENSION`, `ALTER PUBLICATION` and
function `GRANT`s. The default already excludes only Supabase-internal schemas.

**Never pass `-f database/schema.sql` directly.** The CLI truncates the target *before* it connects,
so any failure (bad password, expired auth, network error) destroys the existing schema file.
Always dump to a temp path and move on success.

```bash
mkdir -p database
cp database/schema.sql /tmp/schema_prev.sql 2>/dev/null
set -a; source .env.local; set +a   # or .env — whichever Step B2 found
supabase db dump --db-url "$SUPABASE_DB_URL" -f /tmp/schema_new.sql < /dev/null
[ -s /tmp/schema_new.sql ] \
  && mv /tmp/schema_new.sql database/schema.sql \
  || echo "DUMP FAILED - existing schema.sql left untouched"
```

### Step B4 — diff and confirm

Compare against `/tmp/schema_prev.sql` and summarise for the user: new tables, new columns, removed
columns, new indexes or policies, anything else. If no previous file existed, just confirm creation.
If nothing changed, say the schema is already up to date.

---

## Troubleshooting: a big `schema.sql` conflict with no real change

Before resolving, check whether the diff is *only* the GRANT rendering described above:

```bash
git diff <a> <b> -- database/schema.sql | grep '^[+-]' | grep -v '^[+-][+-]' | grep -v 'GRANT'
```

Empty output means **zero real schema drift** — the conflict is pure formatting. Resolve toward the
CI version (`git checkout --ours database/schema.sql` during a rebase onto the remote), because CI
will rewrite it that way on its next scheduled run anyway.

---

## Keep this skill current

If anything in a run deviates from these instructions — a command changed behaviour, a flag was
renamed, an extra manual step was needed — **propose specific edits to this SKILL.md and wait for
the user's approval before changing it.** Never self-edit without sign-off.
