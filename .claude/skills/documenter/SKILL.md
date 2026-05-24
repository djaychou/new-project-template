---
name: documenter
description: >
  Documents workflows, features, configs, audits, architecture decisions, integrations,
  and database schemas into structured markdown under docs/. Auto-detects doc type,
  checks for existing docs to update vs create new, maintains edit history and archive
  sections. Triggers when user says "document this", "document the X", "write docs for",
  "update the docs", or proactively after completing significant implementation work.
  Also triggers on "/documenter".
user-invokable: true
argument-hint: "[optional: topic or doc type to document, e.g. 'recurring expenses feature']"
metadata:
  author: Djay
  version: 1.1.0
---

# Documenter

## Critical Rules

1. **All docs** must have: created date, summary at top. Edited docs get an Edit History section.
2. **Obsolete content** moves to Archive section (max 4 sentences per archived item explaining what was removed and why).
3. **Include code references** as `file_path:line_number` where helpful.
4. **Optimize for AI agent readability** — structured headers, bullet points, clear hierarchy.
5. **Be thorough but concise** — every detail that matters, zero filler.
6. **Never auto-create subfolders** — always ask the user before creating any new subfolder under `docs/`.
7. **Always confirm target doc** with user before writing, regardless of match strength.
8. **New docs:** Write directly, don't show inline preview. After writing, ask if the user wants edits. **Existing docs:** Show proposed diff before writing.

## Doc Types

| Type | Subfolder | Purpose |
|---|---|---|
| Workflow | `docs/workflows/` | Multi-step processes, data flows, user journeys |
| Feature | `docs/features/` | Feature specs, usage context, purpose, outcome |
| Audit | `docs/audits/` | Security, SEO, code, performance audit results |
| Config | `docs/configs/` | Environment setup, service configs, deployment |
| Decision | `docs/decisions/` | Architecture decisions — context, choice, consequences |
| Integration | `docs/integrations/` | External service docs — auth, limits, gotchas |
| Database | `docs/database/` | Schema, migrations, RLS policies, index rationale |

## Step 1: Classify Doc Type

Auto-detect which of the 7 types fits based on:
- What the user just worked on or is asking about
- Keywords in the request (e.g., "auth flow" → workflow, "added budget alerts" → feature)
- Context from recent code changes

**Present your classification to the user and confirm before proceeding.**

If ambiguous, present top 2 candidates and let the user choose.

## Step 2: Check for Existing Docs

1. Check if the target subfolder exists. If not, ask user: "No `docs/{type}/` folder exists yet. Create it?"
2. If subfolder exists, glob all `.md` files in it.
3. Read the filename and **Summary** section of each candidate.
4. Match against current topic.

### Match Decision

- **Any match found (strong or weak):** Present the matched doc to the user. Show filename, summary, and why you think it matches. Ask: "Found existing doc `{filename}`. Update this one, or create a new doc?"
- **Weak match additionally:** Suggest creating a new doc as the recommended option. "Found `{filename}` which is somewhat related, but I'd recommend creating a new doc. Your call."
- **No match:** Proceed to create mode. Confirm topic and filename with user.

## Step 3: Gather Context

Before writing, collect:
- Relevant source code (read key files, note file paths and line numbers)
- Git history if useful (`git log --oneline -10` for recent changes related to topic)
- Existing related docs (cross-reference potential)
- User's verbal context from the conversation

## Step 4: Write or Edit

### Create Mode

Use the appropriate template from `references/templates.md` for the doc type.

Go ahead and **write the file directly** — do not show a preview inline. After writing, tell the user the file was created and ask if they want any edits to the content.

Filename: kebab-case, descriptive. Confirm with user before creating.

### Edit Mode

1. Read the full existing doc.
2. Determine what's new, changed, or obsolete.
3. **Show the user a preview of changes** — present what will be added, modified, or archived. Format as:
   ```
   ## Proposed Changes to {filename}

   ### Added
   - [new content summary]

   ### Modified
   - [section name]: [what changed]

   ### Archived
   - [content being moved to archive]: [reason, max 4 sentences]
   ```
4. Wait for user approval before writing.
5. Apply changes:
   - Update relevant sections in place
   - Move obsolete content to **Archive** section at bottom
   - Append entry to **Edit History** section

## Step 5: Update Master Index

Each doc subfolder contains a `master.md` file — a central registry of all docs in that folder. After writing or editing a doc, update the folder's `master.md`.

**Only update master.md if the folder already exists.** Do not create folders or master.md files for folders that don't exist.

### Master Index Format

The master.md uses a **dual format** optimized for both AI agents and humans:

1. **AI Agent Index** (HTML comment block at top) — structured `id | primary_files | status` lines for fast machine parsing
2. **Human-Readable Tables** — full detail tables grouped by category
3. **Deprecated/Removed Table** (at bottom) — items removed by user decision, with removal reason

### Column Headers by Doc Type

Each doc type has different table columns reflecting its content:

| Doc Type | Table Columns |
|---|---|
| **Features** | Feature, Description, Date Started, Last Edited, Workflow, Reference Code |
| **Workflows** | Workflow, Description, Trigger, Date Started, Last Edited, Reference Code |
| **Audits** | Audit Name, Type, Date, Scope, Severity Summary, Reference Code |
| **Configs** | Config Name, Service, Date Started, Last Edited, Variables, Reference Code |
| **Decisions** | Decision, Status, Date, Context Summary, Consequences, Reference Code |
| **Integrations** | Service, Purpose, Date Started, Last Edited, Auth Method, Reference Code |
| **Database** | Table/Entity, Purpose, Date Started, Last Edited, Key Relationships, Reference Code |

### Update Rules

- **Adding a new doc:** Add an entry to the AI Agent Index comment block, a card to the human-readable section, and a row to the AI Agent Reference table.
- **Editing a doc:** Update the "Last edited" date in both the human card and AI reference table. Update description if scope changed.
- **Deprecating/removing:** Remove from both the human cards and AI Agent Reference table. Add a row to the "Deprecated / Removed" table with: Date Removed, Reason for Removal, and Last Known Code Reference.
- **Icebox graduation:** When documenting a completed feature that exists in the Feature Icebox (status "not started" or "in progress"), remove that row from the Icebox table and add the feature to both the human-readable cards section and the AI Agent Reference table as an active feature.
- **Always update** the `Last Updated` date in the master.md header.

### Dual Format Structure

Each master.md has two main content areas:

1. **Human-Readable Section** ("For Humans — ...") — features as titled cards grouped by category. Each card has: feature name (bold heading), 2-4 line description as a paragraph, and a metadata line with dates and doc cross-references. No tables — optimized for scanning.
2. **AI Agent Reference Section** — compact tables with one row per feature. Short filenames (not full paths), feature IDs, status, and workflow/doc references. A note at the top clarifies path prefixes. This section also contains reference tables (commands, DB tables, etc.).

## Step 6: Cross-Reference

After writing, check if any existing docs in other subfolders should reference this doc or vice versa. Suggest links but do not auto-edit other docs without user approval.

## Type-Specific Instructions

### Workflows (`docs/workflows/`)
Must contain:
- **Trigger** — what initiates the workflow
- **Steps** — numbered, with actor (user/system/service) per step
- **Data flow** — what data moves between steps, format if relevant
- **Error paths** — what happens when steps fail
- **Code references** — key files and functions involved

Structure for easy sequential reading by AI agents.

### Features (`docs/features/`)
Must contain:
- **Context** — why this feature exists, what problem it solves
- **Workflow integration** — where in the user journey this feature appears
- **Implementation** — key files, components, services involved
- **Purpose and outcome** — what the user gains
- **Constraints** — limits, edge cases, known issues

### Audits (`docs/audits/`)
Must contain:
- **Audit type** — security / SEO / code / performance
- **Scope** — what was audited
- **Findings** — categorized by severity (critical, warning, info)
- **Recommendations** — actionable fixes with priority
- **Results** — metrics, scores, pass/fail

### Configs (`docs/configs/`)
Must contain:
- **Service/tool** — what this config is for
- **Variables** — each variable with purpose, format, default
- **Dependencies** — what breaks if misconfigured
- **Environment differences** — dev vs prod variations

### Decisions (`docs/decisions/`)
Must contain:
- **Context** — what situation prompted the decision
- **Options considered** — alternatives evaluated (brief)
- **Decision** — what was chosen
- **Consequences** — tradeoffs, what this enables/prevents
- **Status** — active / superseded / deprecated

### Integrations (`docs/integrations/`)
Must contain:
- **Service** — name, purpose, why chosen
- **Connection** — how it's configured, auth method
- **Key files** — where integration code lives
- **Rate limits / quotas** — relevant constraints
- **Gotchas** — non-obvious behaviors, known issues

### Database (`docs/database/`)
Must contain:
- **Table/entity** — what it represents
- **Schema** — columns, types, constraints
- **Relationships** — foreign keys, junction tables
- **RLS policies** — access rules
- **Indexes** — what's indexed and why
- **Migration notes** — changes from original schema

## Proactive Documentation Reminder

After completing any significant implementation work in a conversation — features, workflow changes, config changes, integrations, schema changes, or architecture decisions — remind the user:

> "This would be worth documenting. Want me to run `/documenter`?"

Do not auto-run. Just suggest.
