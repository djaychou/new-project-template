# Skill Building Quick Reference

Condensed from "The Complete Guide to Building Skills for Claude" by Anthropic.

## Skill File Structure

```
your-skill-name/
  SKILL.md                    # Required - main skill file
  scripts/                    # Optional - executable code
  references/                 # Optional - documentation
  assets/                     # Optional - templates, fonts, icons
```

## Frontmatter Fields

| Field | Required | Notes |
|-------|----------|-------|
| `name` | Yes | max 64 chars, kebab-case (lowercase/numbers/hyphens), match folder name |
| `description` | Yes | WHAT + WHEN + capabilities, under 1024 chars, **third person**, no XML |
| `user-invokable` | No | Set `true` if user can trigger via `/skill-name` |
| `argument-hint` | No | Hint for arguments when user-invokable |
| `license` | No | MIT, Apache-2.0, etc. |
| `compatibility` | No | Environment requirements, 1-500 chars |
| `metadata` | No | Custom key-value pairs (author, version, mcp-server) |

## Description Formula

```
[What it does] + [When to use it] + [Key capabilities]
```

### Good Examples
- "Analyzes Figma design files and generates developer handoff documentation. Use when user uploads .fig files, asks for 'design specs', 'component documentation', or 'design-to-code handoff'."
- "Manages Linear project workflows including sprint planning, task creation, and status tracking. Use when user mentions 'sprint', 'Linear tasks', 'project planning', or asks to 'create tickets'."

### Bad Examples
- "Helps with projects." (too vague)
- "Creates sophisticated multi-page documentation systems." (missing triggers)
- "Implements the Project entity model with hierarchical relationships." (too technical, no user triggers)

## Security Restrictions

- No XML angle brackets in frontmatter
- No "claude" or "anthropic" in skill name
- No README.md inside skill folder

## Progressive Disclosure (3 Levels)

1. **Frontmatter** -- always loaded in system prompt. Keep minimal.
2. **SKILL.md body** -- loaded when Claude thinks skill is relevant. Full instructions.
3. **Linked files (references/)** -- loaded on demand. Detailed docs.

## Instruction Best Practices

- Be specific: `Run python scripts/validate.py --input {filename}` not "Validate the data"
- Put critical instructions at the top
- Use bullet points and numbered lists
- Include error handling
- Add examples
- Reference bundled files clearly
- Keep SKILL.md body under 500 lines

## Skill Categories

1. **Document & Asset Creation** -- consistent output with embedded style guides, templates, quality checklists
2. **Workflow Automation** -- multi-step processes with validation gates, templates, review suggestions, iterative refinement
3. **MCP Enhancement** -- workflow guidance on top of MCP tools, coordinates multiple MCP calls, embeds domain expertise

## Common Patterns

1. **Sequential workflow orchestration** -- explicit step ordering, dependencies, validation at each stage
2. **Multi-MCP coordination** -- clear phase separation, data passing between MCPs, validation before next phase
3. **Iterative refinement** -- quality criteria, validation scripts, refinement loops, know when to stop
4. **Context-aware tool selection** -- decision criteria, fallback options, transparency about choices
5. **Domain-specific intelligence** -- specialized knowledge beyond tool access, compliance before action

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Won't upload | File not named SKILL.md | Rename (case-sensitive) |
| Invalid frontmatter | Missing `---` delimiters or bad YAML | Check delimiters and formatting |
| Invalid name | Spaces or capitals | Use kebab-case |
| Doesn't trigger | Vague description | Add specific trigger phrases |
| Triggers too often | Too broad | Add negative triggers, be specific |
| Instructions not followed | Too verbose or buried | Move critical rules to top, use bullets |
| Slow/degraded | Too large | Move docs to references/, keep under 500 lines |

## Validation Checklist

- [ ] Folder: kebab-case
- [ ] SKILL.md exists (exact spelling)
- [ ] Frontmatter: `---` delimiters
- [ ] name: kebab-case, matches folder
- [ ] description: includes WHAT + WHEN
- [ ] No XML tags in frontmatter
- [ ] Instructions: clear and actionable
- [ ] Error handling included
- [ ] Examples provided
- [ ] Tested trigger phrases
