# Agent Configuration Reference

Complete reference for all sub-agent frontmatter fields and configuration options.

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier, lowercase letters and hyphens only |
| `description` | Yes | When Claude should delegate to this agent |
| `tools` | No | Allowlist of tools. Inherits all if omitted |
| `disallowedTools` | No | Denylist of tools, removed from inherited set |
| `model` | No | `sonnet`, `opus`, `haiku`, full model ID, or `inherit` (default) |
| `permissionMode` | No | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan` |
| `maxTurns` | No | Maximum agentic turns before stopping |
| `skills` | No | Skills to inject into agent context at startup |
| `mcpServers` | No | MCP servers available to this agent |
| `hooks` | No | Lifecycle hooks scoped to this agent |
| `memory` | No | Persistent memory scope: `user`, `project`, `local` |
| `background` | No | `true` to always run as background task |
| `effort` | No | Override effort: `low`, `medium`, `high`, `max` |
| `isolation` | No | `worktree` for isolated git worktree |

## Available Tools

Built-in Claude Code tools that can be used in the `tools` or `disallowedTools` fields:

- `Read` -- Read files
- `Write` -- Create/overwrite files
- `Edit` -- Edit existing files
- `Bash` -- Run shell commands
- `Grep` -- Search file contents
- `Glob` -- Search file names by pattern
- `Agent` -- Spawn sub-agents (only works for main thread agents via `--agent`)

MCP tools are also available if MCP servers are configured.

### Tool Restriction Patterns

**Read-only** (reviewers, researchers):
```yaml
tools: Read, Grep, Glob, Bash
```

**No file writes** (keep everything else):
```yaml
disallowedTools: Write, Edit
```

**Restrict which agents can be spawned** (main thread only):
```yaml
tools: Agent(worker, researcher), Read, Bash
```

## Scope Priority

When multiple agents share the same name, higher priority wins:

| Priority | Location | Scope |
|----------|----------|-------|
| 1 (highest) | `--agents` CLI flag | Current session only |
| 2 | `.claude/agents/` | Current project |
| 3 | `~/.claude/agents/` | All your projects |
| 4 (lowest) | Plugin `agents/` directory | Where plugin is enabled |

## Memory Scopes

| Scope | Location | Best for |
|-------|----------|----------|
| `user` | `~/.claude/agent-memory/<name>/` | Cross-project knowledge |
| `project` | `.claude/agent-memory/<name>/` | Project-specific, shareable via git |
| `local` | `.claude/agent-memory-local/<name>/` | Project-specific, NOT in git |

When memory is enabled:
- System prompt includes memory read/write instructions
- First 200 lines of `MEMORY.md` in the memory dir are included
- Read, Write, Edit tools are auto-enabled for memory management

## Hook Events (in agent frontmatter)

| Event | Matcher input | When it fires |
|-------|--------------|---------------|
| `PreToolUse` | Tool name | Before agent uses a tool |
| `PostToolUse` | Tool name | After agent uses a tool |
| `Stop` | (none) | When agent finishes (becomes SubagentStop at runtime) |

## Hook Events (in settings.json for main session)

| Event | Matcher input | When it fires |
|-------|--------------|---------------|
| `SubagentStart` | Agent type name | When an agent begins |
| `SubagentStop` | Agent type name | When an agent completes |

## MCP Server Configuration

Reference an existing server by name or define inline:

```yaml
mcpServers:
  - github                    # reuses already-configured server
  - playwright:               # inline definition, scoped to this agent
      type: stdio
      command: npx
      args: ["-y", "@playwright/mcp@latest"]
```

Inline servers connect when the agent starts and disconnect when it finishes.

## Plugin Agent Restrictions

Plugin agents do NOT support:
- `hooks`
- `mcpServers`
- `permissionMode`

Copy the agent to `.claude/agents/` or `~/.claude/agents/` if you need these features.

## Key Constraints

- Agents CANNOT spawn other agents (no nesting)
- Agents receive only their system prompt + basic env details
- CLAUDE.md and project memory still load normally
- Skills must be explicitly listed -- agents don't inherit from parent
- Background agents auto-deny permissions not pre-approved
- Agent transcripts stored at `~/.claude/projects/{project}/{session}/subagents/`
