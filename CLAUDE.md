# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

<!-- TODO: Fill in after project setup -->
<!-- Example:
- `npm run dev` - Start development server
- `npm run build` - Build the production application
- `npm run lint` - Run linting
-->

## Architecture Overview

<!-- TODO: Fill in after user provides the project brief -->
<!-- Include: project description, tech stack, project structure, key patterns, important files -->

Feature index with file pointers: [docs/features/master.md](docs/features/master.md)
Env vars: [docs/configs/environment-variables.md](docs/configs/environment-variables.md)

## Project Setup

When the user provides a project brief for the first time:
1. Fill in the **Development Commands** and **Architecture Overview** sections above based on the brief.
2. Proceed with project scaffolding.

## Agent Rules
Keep responses brief and concise. Sacrifice vocabulary for concision.

## Implementing Code and Directory Rules
1. You have to be organized and structured in your approach while creating files, folders and code.
2. Don't let the page get too big, split up components and maintain file/folder structure.
3. After you implement any action items, ask me to test it manually.
4. Don't run build by default, run typescript checks. Run build when I ask you to.
5. For any changes related to backend or database, always check `database/schema.sql`, the real production schema; trust it over migration files. If it doesn't exist, tell me we need to create it with the `update-schema` skill. Refresh it only with `update-schema`, never by hand.
6. **Search before creating** — always search the codebase/folders for existing functions, components, and utilities before creating new ones. If unsure about reuse, ask for confirmation.
7. **AI-agent-first documentation** — structure docs for AI agent parsing as primary reader. Include code references with file paths and line numbers. Humans are secondary readers.
8. **File length limits** — 700 lines soft limit, 1000 lines hard limit per file. Split into separate files and maintain directory structure when exceeded.
9. While installing any new external packages, get the latest docs from context7 mcp first.
10. When implementing code from external packages, get the latest docs from context7 mcp first.
11. When the user asks to edit an existing feature, find it in `docs/features/master.md` and read its docs before making any changes. If it's a new feature, look at associated features for context.
12. **Secrets** — never print `.env*` contents; load them with `set -a; . ./.env; set +a` instead. Never put server-only secrets (access tokens, service-role keys, DB passwords) in a variable the client bundle can read (`VITE_*`, `NEXT_PUBLIC_*`).
13. **Deploy, then commit** — anything deployed by hand (edge functions, bots, workers) gets committed in the same turn. Otherwise production runs code that exists only on one laptop, and the next deploy from a clean clone silently reverts it. Stage by explicit path, never `git add -A`.
14. When adding a rule to this file, include why it exists, and the incident behind it if there was one.
15. **Before every commit + deploy, run `/session-cleanup`, then `/documenter`** (in that order), and only then commit and push. No commits or pushes without the user's explicit go-ahead; work lands locally first, the user tests, then one batch ships. Why: pushes to the default branch auto-deploy, and a batch that goes out without the cleanup leaves throwaway files and stale docs behind in production (added 2026-10-10, from the Minime project).
