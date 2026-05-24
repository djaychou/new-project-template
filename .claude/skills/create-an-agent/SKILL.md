---
name: create-an-agent
description: >
  Interactive guide for creating Claude Code sub-agents. Walks through
  purpose definition, model/tool selection, system prompt writing, and
  optional configuration (hooks, memory, MCP servers, skills).
  Use when user says "create an agent", "build an agent", "new agent",
  "make a subagent", "/create-an-agent", or "agent creator".
user-invokable: true
argument-hint: "[optional: agent name or purpose description]"
metadata:
  author: speakship
  version: 1.0.0
---

# /create-an-agent -- Sub-Agent Creator Workflow

## Critical Rules

- Agent files are Markdown with YAML frontmatter
- `name` field: lowercase letters and hyphens only
- `description` field: tells Claude WHEN to delegate to this agent -- be specific
- Only `name` and `description` are required fields
- Agents CANNOT spawn other agents (no nesting)
- Agents receive ONLY their system prompt + basic env details, NOT the full Claude Code system prompt
- CLAUDE.md files and project memory still load normally
- Keep system prompts focused -- one agent, one job
- Agent files are flat `.md` files in `.claude/agents/` — no subdirectories per agent
- If the agent needs supporting reference docs, those belong in the **skill** that launches it (under `references/`), NOT alongside the agent file

---

## Instructions

Walk the user through each step. Do not advance until current step is approved.

### Step 1: Define Purpose and Scope

Ask the user:
- What specific task should this agent handle?
- Should it be read-only (research/review) or read-write (can modify files)?
- Should Claude delegate automatically, or only when explicitly asked?

Determine the **scope** (where to save the file):

| Scope | Location | When to use |
|-------|----------|-------------|
| Project | `.claude/agents/` | Specific to this codebase, shareable via git |
| User | `~/.claude/agents/` | Personal, available in all projects |

Recommend **project scope** as default (shareable, version-controlled).

Output a structured definition:

```
Agent: [name]
Purpose: [one sentence]
Scope: project / user
Read-only: yes / no
Auto-delegate: yes / no
```

---

### Step 2: Choose Model and Tools

**Model options:**

| Model | Best for |
|-------|----------|
| `haiku` | Fast, cheap tasks (search, exploration, simple checks) |
| `sonnet` | Balanced (code review, analysis, moderate complexity) |
| `opus` | Complex reasoning (architecture decisions, deep analysis) |
| `inherit` | Same as main conversation (default if omitted) |

**Tool selection strategy:**

- **Read-only agents** (reviewers, researchers): `Read, Grep, Glob, Bash`
- **Editing agents** (fixers, implementers): `Read, Edit, Write, Bash, Grep, Glob`
- **Full access**: Omit `tools` field entirely (inherits all tools)
- Use `disallowedTools` to deny specific tools while keeping everything else

Present a tools recommendation based on Step 1 and get approval.

---

### Step 3: Write the System Prompt

The Markdown body below the frontmatter becomes the agent's system prompt. Write it following these rules:

**Structure:**
```markdown
You are a [role]. [One sentence about specialization].

When invoked:
1. [First action]
2. [Second action]
3. [Third action]

[Domain-specific guidance]:
- [Rule 1]
- [Rule 2]
- [Rule 3]

For each [task], provide:
- [Output item 1]
- [Output item 2]
- [Output item 3]
```

**Writing rules:**
- Lead with the role and specialization
- Provide a clear "When invoked" sequence so the agent knows what to do immediately
- Be specific and actionable -- no vague instructions
- Include what output/format to produce
- Keep it focused -- one agent, one job
- The agent does NOT see the full Claude Code system prompt, so include all necessary context

---

### Step 4: Configure Optional Features

Ask the user if they need any of these. Skip if not relevant.

**A. Persistent Memory**

Lets the agent accumulate knowledge across conversations.

```yaml
memory: project  # or user, local
```

| Scope | Location | Use when |
|-------|----------|----------|
| `project` | `.claude/agent-memory/<name>/` | Knowledge is project-specific, shareable via git |
| `user` | `~/.claude/agent-memory/<name>/` | Knowledge applies across all projects |
| `local` | `.claude/agent-memory-local/<name>/` | Project-specific but NOT in git |

If enabled, add to the system prompt: "Update your agent memory as you discover patterns, conventions, and key decisions."

**B. Permission Mode**

| Mode | Behavior |
|------|----------|
| `default` | Standard permission prompts |
| `acceptEdits` | Auto-accept file edits |
| `dontAsk` | Auto-deny permission prompts (allowed tools still work) |
| `bypassPermissions` | Skip all prompts (use with caution) |
| `plan` | Read-only exploration mode |

**C. Preloaded Skills**

Inject skill content into the agent's context at startup:

```yaml
skills:
  - api-conventions
  - error-handling-patterns
```

Skills are fully injected, not just made available. The agent does NOT inherit skills from the parent conversation.

**D. MCP Servers**

Give the agent access to MCP servers:

```yaml
mcpServers:
  - github          # reference existing server
  - playwright:     # inline definition
      type: stdio
      command: npx
      args: ["-y", "@playwright/mcp@latest"]
```

**E. Hooks**

Add lifecycle hooks for validation or side effects:

```yaml
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate.sh"
```

**F. Other Options**

- `maxTurns`: Limit agentic turns before stopping
- `background: true`: Always run as background task
- `effort`: Override effort level (`low`, `medium`, `high`, `max`)
- `isolation: worktree`: Run in isolated git worktree

For detailed reference on all options, see [references/agent-config-reference.md](references/agent-config-reference.md).

---

### Step 5: Validate

Run through this checklist:

**Structure:**
- [ ] File is Markdown with YAML frontmatter using `---` delimiters
- [ ] `name` field: lowercase letters and hyphens only
- [ ] `description` clearly states WHEN Claude should delegate
- [ ] Saved to correct scope (`.claude/agents/` or `~/.claude/agents/`)

**System Prompt Quality:**
- [ ] Starts with clear role definition
- [ ] Has "When invoked" action sequence
- [ ] Instructions are specific and actionable
- [ ] Output format specified
- [ ] Focused on one task

**Tools & Permissions:**
- [ ] Tool access matches the agent's purpose (not over-permissioned)
- [ ] Permission mode appropriate for the task

**Triggers (test mentally):**
- [ ] Description would cause Claude to delegate on obvious tasks
- [ ] Description would NOT cause delegation on unrelated tasks
- [ ] If "use proactively" is in description, that behavior is desired

---

### Step 6: Create the Agent File

Write the file to the chosen scope location:
- Project: `.claude/agents/{name}.md`
- User: `~/.claude/agents/{name}.md`

After creation, confirm with:
"Agent `{name}` created at `{path}`. Restart your session or run `/agents` to load it. Test by saying: '{example trigger phrase}'"

---

### Step 7: Export to AI Toolkit (optional)

After the agent is created, ask the user:

> "Would you like to add this agent to your AI toolkit so it's portable to other projects, or keep it local to this project?"

**If the user wants it portable (toolkit)**:
1. Check if `~/ai-toolkit` exists
2. If it exists, copy the agent file to `~/ai-toolkit/agents/{agent-name}.md`
3. Update `~/ai-toolkit/ai-config/toolkit.yml` — add the agent under the appropriate category (`generic` or `nextjs`)
4. Commit and push the toolkit repo
5. Proceed to Step 8 with scope = **portable**

**If the user wants it local (project-specific)**:
1. Agent stays in `.claude/agents/` only
2. Proceed to Step 8 with scope = **project-specific**

If `~/ai-toolkit` does not exist, inform the user:
> "AI toolkit repo not found at ~/ai-toolkit. Clone it first: `git clone git@github.com:djaychou/ai-toolkit.git ~/ai-toolkit`"

---

### Step 8: Update Inventory Doc

Update `ai-config/skills-and-agents-inventory.md` in the current project. This file tracks all skills and agents with two sections:

**If scope is portable** — add an entry under `## Portable Agents (from ai-toolkit)` with:
- Name, purpose (1-2 lines), file path, model, tools, last modified date, launched by, and brief workflow steps

**If scope is project-specific** — add an entry under `## Project-Specific Agents (local only)` with the same fields

Use the existing entries as a template for formatting. If the inventory file doesn't exist, create it at `ai-config/skills-and-agents-inventory.md` with both section headers.

---

## Examples

### Example 1: Code Reviewer
User says: "Create an agent for code reviews"
1. Purpose: review code for quality/security, read-only
2. Model: `sonnet`, Tools: `Read, Grep, Glob, Bash`
3. System prompt with review checklist and output format
4. Optional: `memory: project` to learn codebase patterns
5. Validate and create at `.claude/agents/code-reviewer.md`

### Example 2: Test Runner
User says: "Build an agent to run and fix tests"
1. Purpose: run tests, diagnose failures, fix code, read-write
2. Model: `inherit`, Tools: all (omit field)
3. System prompt with test-fix-verify loop
4. Optional: `maxTurns: 20` to prevent runaway loops
5. Validate and create at `.claude/agents/test-runner.md`

### Example 3: Background Researcher
User says: "Make an agent that researches in the background"
1. Purpose: explore codebase questions without blocking, read-only
2. Model: `haiku`, Tools: `Read, Grep, Glob, Bash`
3. System prompt focused on thorough exploration and concise summaries
4. Optional: `background: true`
5. Validate and create at `.claude/agents/researcher.md`

## Common Issues

### Agent doesn't get used
- Description may be too vague -- add specific trigger phrases
- Add "Use proactively" to encourage auto-delegation
- Check: does the description match what users actually say?

### Agent is too slow
- Switch to `haiku` model for simple tasks
- Reduce tool access to only what's needed
- Set `maxTurns` to prevent long-running agents

### Agent can't access needed tools
- Check `tools` field -- MCP tools are excluded when tools is specified
- Use `disallowedTools` instead to keep everything except specific tools
- Verify MCP servers are configured in `mcpServers` field

### Agent doesn't remember context
- Enable `memory` field with appropriate scope
- Add memory instructions to the system prompt
- Ask agent to "check your memory" before starting work
