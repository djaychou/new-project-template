---
name: create-a-skill
description: >
  Interactive guide for creating new Claude Code skills. Walks through use case
  definition, frontmatter generation, instruction writing, and validation.
  Use when user says "create a skill", "build a skill", "new skill", "make a skill",
  "/create-a-skill", or "skill creator".
user-invokable: true
argument-hint: "[optional: skill name or use case description]"
metadata:
  author: speakship
  version: 1.0.0
---

# /create-a-skill -- Skill Creator Workflow

## Instructions

Walk the user through building a production-quality Claude Code skill using the step-by-step workflow below. Each step is a validation gate -- do not advance until the current step is complete.

## Important Rules

- Skill folder MUST use kebab-case (no spaces, underscores, or capitals)
- Main file MUST be named exactly `SKILL.md` (case-sensitive)
- Supporting files (templates, checklists, guides) MUST go in a `references/` subdirectory — NEVER place loose `.md` files alongside `SKILL.md` in the skill root
- Scripts MUST go in a `scripts/` subdirectory, assets in `assets/`
- The only file allowed at the skill root level is `SKILL.md` — everything else goes in a subdirectory
- YAML frontmatter MUST use `---` delimiters
- `name` field: max 64 chars, kebab-case only (lowercase letters, numbers, hyphens), must match folder name
- `description` field: MUST include WHAT the skill does + WHEN to use it (trigger conditions)
- Description MUST be written in **third person** ("Processes files..." not "I help you..." or "You can use this to...")
- Description MUST be under 1024 characters, no XML tags
- No XML angle brackets anywhere in frontmatter
- No "claude" or "anthropic" in the skill name
- No README.md inside the skill folder -- all docs go in SKILL.md or references/
- Keep SKILL.md body under **500 lines** -- move detailed docs to `references/`
- References MUST be **one level deep** from SKILL.md (no nested reference chains)
- Only add context Claude doesn't already have -- Claude is already very smart

---

### Step 1: Define Use Cases

Ask the user to describe 2-3 concrete use cases the skill should enable.

Guide with these questions:
- What does a user want to accomplish?
- What multi-step workflows does this require?
- Which tools are needed (built-in or MCP)?
- What domain knowledge or best practices should be embedded?

Identify which category the skill fits:
1. **Document & Asset Creation** -- consistent, high-quality output (templates, style guides, quality checklists)
2. **Workflow Automation** -- multi-step processes with validation gates and iterative refinement
3. **MCP Enhancement** -- workflow guidance layered on top of MCP tool access (coordinates MCP calls, embeds domain expertise, provides context, handles errors)

Output a structured use case definition:

```
Use Case: [Name]
Trigger: User says "[phrase 1]" or "[phrase 2]"
Steps:
1. [Step]
2. [Step]
Result: [Expected outcome]
```

---

### Step 2: Generate Frontmatter

Create the YAML frontmatter based on Step 1. Follow this structure:

```yaml
---
name: {kebab-case-name}
description: >
  {What it does} + {When to use it with specific trigger phrases users would say}.
  {Key capabilities in 1-2 sentences}.
user-invokable: true
argument-hint: "[optional: description of expected arguments]"
metadata:
  author: {author}
  version: 1.0.0
---
```

**Description quality checklist:**
- Is it written in **third person**? ("Processes files..." not "I help you..." or "You can use this...")
- Is it specific and actionable? ("Helps with projects" will NOT work)
- Does it include trigger phrases users would actually say?
- Does it mention relevant file types if applicable?
- Does it explain WHAT + WHEN clearly?
- Is it under 1024 characters?

Present the frontmatter to the user for approval before proceeding.

---

### Step 3: Write Main Instructions

Write the SKILL.md body in Markdown following these best practices:

**Structure:**
```markdown
# Skill Name

## Instructions
### Step 1: [First Major Step]
Clear explanation of what happens.

### Step 2: [Next Step]
...

## Examples
### Example 1: [Common scenario]
User says: "..."
Actions: ...
Result: ...

## Common Issues
### [Error type]
If you see "[error]":
1. [Fix step]
2. [Fix step]
```

**Writing rules:**
- Be specific and actionable ("Run `python scripts/validate.py --input {filename}`" not "Validate the data")
- Put critical instructions at the TOP
- Use `## Important` or `## Critical` headers for must-follow rules
- Use bullet points and numbered lists
- Include error handling for common failure modes
- Add examples showing common and edge-case scenarios
- Reference bundled files clearly (e.g., "consult `references/api-patterns.md` for...")

---

### Step 4: Add Supporting Files (if needed)

Determine if the skill needs:
- `references/` -- Documentation, API guides, examples loaded on demand
- `scripts/` -- Executable code (Python, Bash, etc.)
- `assets/` -- Templates, fonts, icons used in output

For each supporting file, explain its purpose and how the SKILL.md references it.

Keep the progressive disclosure principle:
- **Level 1 (frontmatter):** Always loaded -- just enough for Claude to decide when to use the skill
- **Level 2 (SKILL.md body):** Loaded when skill is relevant -- full instructions
- **Level 3 (references/):** Loaded on demand -- detailed docs Claude navigates as needed

---

### Step 5: Validate

Run through this checklist with the user:

**Structure:**
- [ ] Folder named in kebab-case
- [ ] SKILL.md file exists (exact spelling)
- [ ] YAML frontmatter has `---` delimiters
- [ ] `name` field: kebab-case, no spaces, no capitals
- [ ] `description` includes WHAT and WHEN
- [ ] No XML tags anywhere in frontmatter

**Quality:**
- [ ] Instructions are clear and actionable
- [ ] Error handling included
- [ ] Examples provided
- [ ] References clearly linked

**Triggers (test mentally):**
- [ ] Would trigger on obvious tasks
- [ ] Would trigger on paraphrased requests
- [ ] Would NOT trigger on unrelated topics

---

### Step 6: Create the Skill Files

Write all files to `.claude/skills/{skill-name}/`:
1. `SKILL.md` with frontmatter + instructions
2. Any `references/` files
3. Any `scripts/` files

After creation, confirm with: "Skill `{name}` created at `.claude/skills/{name}/`. Test it by asking Claude: '{example trigger phrase}'"

---

### Step 7: Export to AI Toolkit (optional)

After the skill is created and tested, ask the user:

> "Would you like to add this skill to your AI toolkit so it's portable to other projects, or keep it local to this project?"

**If the user wants it portable (toolkit)**:
1. Check if `~/ai-toolkit` exists
2. If it exists, copy the skill directory to `~/ai-toolkit/skills/{skill-name}/`
3. Update `~/ai-toolkit/ai-config/toolkit.yml` — add the skill under the appropriate category (`generic` or `nextjs`)
4. Commit and push the toolkit repo
5. Proceed to Step 8 with scope = **portable**

**If the user wants it local (project-specific)**:
1. Skill stays in `.claude/skills/` only
2. Proceed to Step 8 with scope = **project-specific**

If `~/ai-toolkit` does not exist, inform the user:
> "AI toolkit repo not found at ~/ai-toolkit. Clone it first: `git clone git@github.com:djaychou/ai-toolkit.git ~/ai-toolkit`"

---

### Step 8: Update Inventory Doc

Update `ai-config/skills-and-agents-inventory.md` in the current project. This file tracks all skills and agents with two sections:

**If scope is portable** — add an entry under `## Portable Skills (from ai-toolkit)` with:
- Name, purpose (1-2 lines), file path, last modified date, workflows used in, and brief workflow steps

**If scope is project-specific** — add an entry under `## Project-Specific Skills (local only)` with the same fields

Use the existing entries as a template for formatting. If the inventory file doesn't exist, create it at `ai-config/skills-and-agents-inventory.md` with both section headers.

---

## Examples

### Example 1: User wants a code review skill
User says: "Create a skill for code reviews"
1. Define use cases (PR review, security audit, performance check)
2. Generate frontmatter with triggers like "review this PR", "code review", "check my code"
3. Write step-by-step review instructions with checklists
4. Add `references/review-checklist.md` for detailed criteria
5. Validate and create files

### Example 2: User wants a deployment skill
User says: "Build a skill for deploying to Vercel"
1. Define use cases (preview deploy, production deploy, rollback)
2. Generate frontmatter with triggers like "deploy", "push to production", "vercel deploy"
3. Write sequential workflow with validation gates
4. Include error handling for common Vercel failures
5. Validate and create files

## Common Issues

### Skill doesn't trigger
- Description may be too vague -- add specific trigger phrases
- Description may be missing the WHEN component
- Check: does it include phrases users would actually say?

### Skill triggers too often
- Add negative triggers: "Do NOT use for [unrelated thing]"
- Be more specific in the description scope
- Clarify what the skill is NOT for

### Instructions not followed
- Instructions may be too verbose -- use bullet points
- Critical rules may be buried -- move them to the top
- Language may be ambiguous -- be specific and actionable

### Large context issues
- Move detailed docs from SKILL.md to `references/`
- Keep SKILL.md under 500 lines
- Link to references instead of inlining content
