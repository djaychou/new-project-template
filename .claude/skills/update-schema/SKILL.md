---
name: update-schema
description: >
  Updates database/schema.sql to match the current production Supabase schema
  using the Supabase CLI. Links the project if not already linked, then runs
  supabase db pull to dump the schema. Triggers when user says "update schema",
  "sync schema", "refresh schema", or "dump schema".
user-invokable: true
---

# Update Schema

## Instructions

Update `database/schema.sql` to reflect the current production Supabase database using the Supabase CLI.

### Step 1: Check Supabase CLI

Verify the Supabase CLI is installed:

```bash
supabase --version
```

If not installed, tell the user to install it (`brew install supabase/tap/supabase` on macOS or `npx supabase`).

### Step 2: Check project link

Check if the project is already linked:

```bash
cat supabase/.temp/project-ref 2>/dev/null
```

If no link exists, ask the user for their Supabase project ref (found in the Supabase dashboard URL: `https://supabase.com/dashboard/project/<project-ref>`), then link it:

```bash
supabase link --project-ref <project-ref>
```

This will prompt for the database password. Let the user enter it.

### Step 3: Pull the schema

```bash
supabase db pull --schema public > database/schema.sql
```

If the `database/` directory doesn't exist, create it first.

### Step 4: Diff and confirm

Before finalizing, show the user a summary of what changed compared to the previous `database/schema.sql`:
- New tables added
- New columns added
- Columns removed
- New indexes or policies
- Any other differences

If no previous schema file existed, just confirm the new file was created.

If no changes detected, tell the user the schema is already up to date.

### Step 5: Confirm completion

Tell the user the schema has been updated and show a short summary of changes.
