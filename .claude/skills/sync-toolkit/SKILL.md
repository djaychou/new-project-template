---
name: sync-toolkit
description: >
  Sync skills and agents between the current project and the ai-toolkit repo.
  Detects new or updated items, exports them, commits, and pushes. Also pulls
  latest from the toolkit into the current project. Triggers on: "/sync-toolkit",
  "sync toolkit", "update toolkit", "export to toolkit", "pull from toolkit".
user-invokable: true
argument-hint: "[push|pull|status] [--skill name] [--agent name]"
metadata:
  author: speakship
  version: 1.0.0
---

# /sync-toolkit

Keeps the ai-toolkit repo (`~/ai-toolkit`) in sync with skills and agents in the current project.

## Important Rules

- The toolkit repo default path is `~/ai-toolkit`. If it doesn't exist, tell the user to clone it first.
- NEVER sync items from the `project-specific` category (check `toolkit.yml`)
- Always fetch and pull the toolkit repo before making changes
- Always confirm with the user before overwriting files
- Supporting files go in `references/` — never place loose files alongside `SKILL.md`

## Modes

### `push` (default if no argument) — Export from project to toolkit

1. **Pre-flight checks**:
   - Verify `~/ai-toolkit` exists
   - Run `git -C ~/ai-toolkit fetch origin`
   - Check if local is behind remote — if so, run `git -C ~/ai-toolkit pull --ff-only`
   - Check for uncommitted changes — if any, abort with message
   - Read `~/ai-toolkit/ai-config/toolkit.yml` to know what's already tracked

2. **Scan for changes**:
   - Compare `.claude/skills/` in the current project against `~/ai-toolkit/skills/`
   - Compare `.claude/agents/` against `~/ai-toolkit/agents/`
   - For each item, classify as:
     - **NEW** — exists in project but not in toolkit
     - **UPDATED** — exists in both, but project version is newer (compare file contents, not just timestamps)
     - **UNCHANGED** — identical in both
   - Skip items in the `project-specific` category from `toolkit.yml`
   - Skip the `archive/` directory

3. **Present findings**:
   - Show a summary table: item name, type (skill/agent), status (NEW/UPDATED/UNCHANGED)
   - Ask the user which items to export (default: all NEW and UPDATED)

4. **Export**:
   - Copy selected skills from `.claude/skills/{name}/` to `~/ai-toolkit/skills/{name}/`
   - Copy selected agents from `.claude/agents/{name}.md` to `~/ai-toolkit/agents/{name}.md`

5. **Update toolkit.yml** (if new items were added):
   - Ask the user which category the new item belongs to (`generic` or `nextjs`)
   - Add the item to the correct category in `~/ai-toolkit/ai-config/toolkit.yml`
   - Also update the dependency map in `references/dependencies.md` if the item has dependencies

6. **Commit and push**:
   - Stage all changes in `~/ai-toolkit`
   - Write a descriptive commit message listing what was synced
   - Push to origin
   - Confirm success

7. **Update inventory doc**:
   - If `ai-config/skills-and-agents-inventory.md` exists in the project:
     - For each UPDATED item, find its entry and update the `**Last modified**` date to today
     - For each NEW item, add an entry in the appropriate section (Portable Skills/Agents) using the existing format
     - Update the "Last synced from ai-toolkit" date in the header to today

### `pull` — Pull latest from toolkit into project

1. **Pre-flight**: fetch and pull `~/ai-toolkit` to latest
2. **Scan**: compare toolkit items against project's `.claude/skills/` and `.claude/agents/`
3. **Present**: show items that are newer in the toolkit than the project
4. **Install**: copy selected items into the project (same as agent install flow)
5. **Update inventory doc**:
   - If `ai-config/skills-and-agents-inventory.md` exists in the project:
     - For each updated item, update the `**Last modified**` date
     - For each new item, add an entry in the Portable section
     - Update the "Last synced from ai-toolkit" date in the header to today
     - Update the portable skills/agents count in the section header

### `status` — Show sync status without making changes

1. Run the same scan as `push`
2. Display the summary table
3. Show when the project was last synced (from inventory doc header)
4. Exit without modifying anything

## Dependency Reference

When exporting, warn if an item's dependencies aren't also in the toolkit:

| Skill | Requires agents |
|-------|----------------|
| `optimize-seo` | `seo-optimizer` |
| `security-audit` | All `security-*` agents |
| `feature-interview` | `feature-planner` |

| Agent | Requires skills |
|-------|----------------|
| `seo-optimizer` | `seo`, `seo-technical`, `seo-content`, `seo-schema`, `seo-images`, `seo-geo`, `optimize-seo` |
| `feature-planner` | `feature-interview` |

## Examples

### Export new skill to toolkit
```
User: /sync-toolkit
Agent: Scans project, finds new skill "my-custom-skill"
Agent: "Found 1 new skill: my-custom-skill. Export to toolkit?"
User: yes
Agent: "Which category? (generic/nextjs)"
User: generic
Agent: Copies, updates toolkit.yml, commits, pushes
```

### Pull latest into project
```
User: /sync-toolkit pull
Agent: Pulls toolkit repo, finds 2 updated skills
Agent: "2 skills are newer in the toolkit: documenter, security-audit. Update?"
User: yes
Agent: Copies into .claude/skills/, confirms
```

### Check status
```
User: /sync-toolkit status
Agent: Shows table of all items with sync status, no changes made
```
