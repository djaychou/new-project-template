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

## Project Setup

When the user provides a project brief for the first time:
1. Fill in the **Development Commands** and **Architecture Overview** sections above based on the brief.
2. Run `/sync-toolkit` to pull the latest skills and agents from `~/ai-toolkit`.
3. Proceed with project scaffolding.

## Agent Rules
Keep responses brief and concise. Sacrifice vocabulary for concision.

## Implementing Code and Directory Rules
1. You have to be organized and structured in your approach while creating files, folders and code.
2. Don't let the page get too big, split up components and maintain file/folder structure.
3. After you implement any action items, ask me to test it manually.
4. Don't run build by default, run typescript checks. Run build when I ask you to.
5. For any changes related to backend or database, always check the schema.sql in database folder. If it doesn't exist, tell me that we need to create it and we can use update-schema skill for this.
6. **Search before creating** — always search the codebase/folders for existing functions, components, and utilities before creating new ones. If unsure about reuse, ask for confirmation.
7. **AI-agent-first documentation** — structure docs for AI agent parsing as primary reader. Include code references with file paths and line numbers. Humans are secondary readers.
8. **File length limits** — 700 lines soft limit, 1000 lines hard limit per file. Split into separate files and maintain directory structure when exceeded.
9. While installing any new external packages, get the latest docs from context7 mcp first.
10. When implementing code from external packages, get the latest docs from context7 mcp first.
