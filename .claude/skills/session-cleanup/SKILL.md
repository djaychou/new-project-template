---
name: session-cleanup
description: >
  Cleans up the leftovers of the current session's own work — unused exports,
  functions and imports in files the session created or changed, finished or
  broken one-off scripts, temp files, scratchpad files, and stale references in
  docs and memory. Scoped strictly to this session, never the whole repo, and
  never touches other sessions' uncommitted work. Triggers when the user says
  "perform a clean up", "clean up after yourself", "remove unused and dead code",
  "remove temp files", "tidy up this session's work", or "/session-cleanup".
  Do NOT use for whole-repo dead-code audits (use code-audit for that).
user-invokable: true
argument-hint: "[optional: narrow the scope, e.g. 'only the locations work']"
metadata:
  author: djaychou
  version: 1.0.0
---

# Session Cleanup

Removes what THIS session left behind. Everything outside the session's footprint is out of scope,
even if it looks dead — a whole-repo sweep is the `code-audit` skill's job.

## Critical Rules

1. **Nothing is deleted before the user approves the list.** Present first, act second.
2. **Irreversible removals get their own explicit yes** — untracked files, gitignored data, rollback snapshots,
   anything git can't bring back. "Go ahead" on the list does not cover an item you flagged as irreversible
   and the user didn't answer; ask again.
3. **Only the session's footprint.** Several sessions may share the working tree. Never touch a file another
   session created or has uncommitted edits in; for a file both touched, only your hunks (`git diff -- <file>`).
4. **Check live data before removing compatibility code.** A branch that handles an old format stays while
   any record in that format still exists — query for it.
5. **Delete with literal paths.** `rm -rf "$VAR"/*` is blocked by a safety check; write the absolute path
   (or `"${VAR:?}"`). Tracked files go through `git rm` so history keeps them.
6. **Commit only if the user asks**, and then by explicit pathspec (`git commit -- <paths>`), never a bare
   commit — other sessions stage their own work in the same index.

## Instructions

### Step 1: Map the session's footprint

Build the list of everything this session produced, from the conversation itself:
- Files created or edited (Write / Edit calls, `git mv`, generated files).
- Commits made this session (`git log --format='%h %s' --since=<session start>`; match subjects you wrote).
- Migrations, scripts and one-off tools written for the work.
- Temp and by-product files: lock files from ad-hoc tool runs (e.g. `deno.lock`), downloaded data,
  `*.tsbuildinfo` you caused, local snapshot folders, the scratchpad directory.
- Docs and memory files the session wrote that reference any of the above.

If the conversation was summarized, use the summary's file list; if that is not enough, grep the session
transcript (its path is in the summary) for `file_path`.

Then run `git status --short` and set aside anything **not** in the footprint — that belongs to someone else.

### Step 2: Find what's dead, within the footprint only

| Look for | How |
|---|---|
| Unused imports / locals | The project's type check and lint (`npx tsc --noEmit`, `npm run lint`) — report only hits in footprint files |
| Unused exports | For each export in a footprint file, count references across the repo excluding its own file. Zero outside + used inside → drop the `export`; unused everywhere → remove it |
| Dead functions / branches / types | Code paths made unreachable by the session's changes (a dropped column, a replaced helper). Rule 4 before removing compatibility code |
| Finished one-off scripts | Backfills, migrations helpers, generators whose work is applied — especially ones that would now FAIL (they read columns the session dropped). Keep anything a fresh setup still needs (seed scripts and their helpers) |
| Temp files | Rule 2 for anything untracked or gitignored |
| Stale references | Comments, docs, memory and index files pointing at things you're about to remove |

### Step 3: Present the list and stop

Group it, one line of reason per item:
1. Code (unused exports / functions / imports / dead branches)
2. Scripts (what each did, why it's done or broken)
3. Temp files
4. Irreversible items — flagged separately, each needing its own yes
5. Kept on purpose — what looks dead but stays, and why (e.g. "legacy undo branch: 1 old snapshot still exists")
6. Not mine, left alone — other sessions' files you noticed

Then wait for approval.

### Step 4: Remove

- Tracked files: `git rm -q <paths>`. Untracked: `rm` with literal paths.
- Scratchpad: empty it with its absolute path.
- Code edits: smallest change (drop an `export`, delete the function, fix the comment).

### Step 5: Fix references and verify

- Update docs, master indexes and memory files that pointed at removed items (say where history lives
  instead, e.g. "removed 2026-09-30, in git history at `<commit>`").
- Run the project's type check and lint; `node --check` any remaining scripts in the touched folders.
- Confirm nothing outside the footprint changed: `git status --short` against Step 1.

### Step 6: Report

What was removed, what was kept and why, verification results, and what still needs the user (unanswered
irreversible items, commit if they want one).

## Examples

### Example 1: After a multi-phase feature
User says: "Let's perform a clean up. Remove any unused and dead code, temp files, scripts, functions,
imports and anything that is unused."
Actions: footprint = the feature's files, migrations and `scripts/geo/*`; type check and lint clean; two
exports used only in their own file; four one-off scripts done (three would now fail on dropped columns);
`deno.lock` from ad-hoc checks; local rollback snapshots. The list is presented, the snapshots flagged
irreversible. After "go ahead": `git rm` the scripts, drop the two `export`s, fix comments and doc links,
empty the scratchpad, verify. The snapshots are left until the user answers separately.
Result: 800 lines removed, 0 lint errors, other sessions' files untouched.

### Example 2: Abandoned approach
User says: "clean up what we tried with the arrays approach"
Actions: footprint narrowed to the files from that attempt; revert or remove them; check nothing else
imports them; verify.

## Common Issues

### A candidate is imported by a file outside the footprint
It is not dead. Leave it, and list it under "Kept on purpose".

### The type check fails after a removal
Something still used it: restore it (`git checkout -- <file>` for a tracked file) and move it to "Kept".

### The rm command is blocked by the safety check
The path used a shell variable. Re-run with the literal absolute path; don't work around the check.

### Another session's hunks are in a file you edited
Only remove your own hunks. If a commit is requested, stage just your hunk (build a patch against HEAD and
`git apply --cached`), or tell the user and let that session commit its part.
