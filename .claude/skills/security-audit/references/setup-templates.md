# Security Audit — Setup Templates

This file contains the file list and locations for the security audit system.
When the `/security-audit` skill detects missing files, it should create them from the source files in this project.

## Two types of dependencies

This system has two separate types of dependencies:

### 1. Project-local files (copied per project)
These live inside the project directory and must be copied to each new project.

### 2. Global Claude Code plugins (installed once, available everywhere)
These are installed via `/plugin` inside Claude Code. They are NOT files in the project directory — they're global plugins stored in Claude Code's internal config (`~/.claude/`). They're available across all projects automatically. You only install them once.

## Project-Local Files

### Skill
- `.claude/skills/security-audit/SKILL.md` — entry point skill
- `.claude/skills/security-audit/setup-templates.md` — this file

### Agents
- `.claude/agents/security-audit-orchestrator.md` — pipeline coordinator
- `.claude/agents/security-recon.md` — Phase 1: reconnaissance
- `.claude/agents/security-planner.md` — Phase 2: audit planning
- `.claude/agents/security-reviewer.md` — Phase 3: main security review
- `.claude/agents/security-specialist-static-analysis.md` — Phase 4: static analysis
- `.claude/agents/security-specialist-insecure-defaults.md` — Phase 4: insecure defaults
- `.claude/agents/security-specialist-supply-chain.md` — Phase 4: supply chain
- `.claude/agents/security-specialist-sharp-edges.md` — Phase 4: sharp edges
- `.claude/agents/security-specialist-ci-automation.md` — Phase 4: CI/CD automation
- `.claude/agents/security-specialist-custom.md` — Phase 4: custom pass template
- `.claude/agents/security-reporter.md` — Phase 5: final report

### Config
- `docs/security-audits/audit-config.yml` — optional user configuration

## Setup Instructions

### Step 1: Copy project-local files (per project)

1. Copy `.claude/skills/security-audit/` to the target project's `.claude/skills/security-audit/`
2. Copy all `security-*.md` files from `.claude/agents/` to the target project's `.claude/agents/`
3. Create `docs/security-audits/` directory in the target project
4. Optionally copy `docs/security-audits/audit-config.yml` as a starting template

### Step 2: Install global plugins (one-time, all projects)

These only need to be installed once. If you've already installed them for another project, skip this step.

**Sentry skills:**
```bash
git clone https://github.com/getsentry/skills.git ~/sentry-skills
claude plugin marketplace add ~/sentry-skills
claude plugin install sentry-skills
```

**Trail of Bits plugins (run inside Claude Code):**
```
/plugin marketplace add trailofbits/skills
/plugin menu
```
Install these from the plugin menu:
- `audit-context-building` (required)
- `static-analysis` (required)
- `insecure-defaults` (required)
- `supply-chain-risk-auditor` (required)
- `sharp-edges` (required)
- `differential-review` (required)
- `agentic-actions-auditor` (optional — for CI/CD automation pass)

### How to verify plugins are installed

Global plugins appear in the available skills list that Claude Code shows in every conversation (in the `<system-reminder>` block). You can also check via `/plugin menu`. The `/security-audit` skill checks for these automatically before launching the audit.
