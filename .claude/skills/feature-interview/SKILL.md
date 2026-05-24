---
name: feature-interview
description: >
  Interactive interview skill for planning new features. Asks targeted questions,
  collects user answers, and writes a feature requirements brief to docs/features/.
  Use proactively when the user wants to plan, brainstorm, ideate, or implement
  a new feature. Triggers on: "plan a feature", "new feature", "I want to build",
  "brainstorm", "ideate", "let's add", "feature idea", "add a feature".
user-invokable: true
argument-hint: "[optional: brief description of the feature idea]"
metadata:
  author: Djay
  version: 2.0.0
---

# Feature Interview

You are conducting a focused interview to understand a new feature the user wants to build. Your job is to ask smart questions, collect answers, and write a requirements brief. You do NOT research external technologies, recommend packages, or produce implementation plans — that happens later via the feature-planner agent.

## Step 1: Understand the Feature

If the user provided a feature description (via argument or in conversation), use it. Otherwise, ask them to describe what they want to build.

## Step 2: Light Codebase Reading (for smarter questions)

Before asking questions, read relevant codebase files so your questions are targeted rather than generic. For example, if the feature touches message handling, read `messageHandler.js` to understand the current flow.

Keep track of which files and lines you read — you'll include these as references in the brief so the agent doesn't have to re-discover them.

## Step 3: Ask Interview Questions

Follow these rules:

- Ask a **MAXIMUM of 7 questions** in total
- **Tailor every question** to the specific feature described — no generic templates
- Cover these areas (weave naturally, not as separate sections):
  - What the feature does and why it matters
  - Who the end user is and how they'll interact with it
  - Expected workflow (what triggers it, what happens, what's the output)
  - Scope boundaries: identify natural expansion points and ask where to draw the line. For example, if they want receipt scanning, ask "just the total, or line items too?" — don't ask vague "what's out of scope?"
- Present all questions at once, numbered, so the user can answer in one pass
- After receiving answers, ask up to **2 follow-up questions ONLY** if something is unclear or contradictory. If everything is clear, move on immediately.

## Step 4: Write Requirements Brief

After collecting all answers, write the brief to `docs/features/[feature-name]-brief-[today's date].md`:

```markdown
# Feature Brief: [Feature Name]

**Created:** [today's date]
**Status:** not started

## 1. Feature Summary
- **What:** One-paragraph description
- **Why:** Problem it solves / value it adds
- **Who:** End users affected

## 2. Scope
- **Included:** [bulleted list of what's in scope]
- **Excluded (future):** [bulleted list of what's explicitly out of scope for now]

## 3. User Requirements
- Key requirements from interview (bulleted)
- Expected user workflow (numbered steps)
- Success criteria

## 4. Constraints
- Relevant CLAUDE.md constraints (cost: $0-10/mo, perf: <3s logging/<2s dashboard, scale: 10 users/group)
- Any user-specified constraints from the interview

## 5. Code References

Files read during interview research. Provided as context for the feature-planner agent.

- `[file path]:[line numbers]` — [brief note on what's relevant here]
- ...

## 6. Open Questions
- Any unresolved decisions or ambiguities

---
*Requirements phase complete. Tech research will be appended by the feature-planner agent. Implementation plan will be added after agent research.*
```

## Step 5: Add to Feature Icebox

After writing the brief, add a row to the **Feature Icebox** table in `docs/features/master.md`.

Insert the row **above** the `<!-- Add rows above this line -->` comment:

```
| [today's date] | [Feature Name] | [2-4 line description] | not started | [today's date] |
```

Also update the `Last Updated` date in the master.md header.

## Step 6: Present Summary

After writing the file and updating the icebox, present a summary to the main conversation in this exact format:

> **Here's my understanding of [Feature Name]:**
> - **What:** ...
> - **Why:** ...
> - **Who:** ...
> - **Workflow:** ...
> - **Included:** ...
> - **Excluded (future):** ...
>
> **Brief saved to `docs/features/[feature-name]-brief.md`**
> **Added to Feature Icebox in `docs/features/master.md` (status: not started)**
>
> **Does this capture it correctly?**

Then tell the user they can:
- Edit any details (you'll update the brief file directly)
- Move forward (which will launch the feature-planner agent to research and append implementation details)

## Important Rules

- NEVER modify existing source code
- NEVER research external technologies or recommend packages — that's the agent's job
- ALWAYS read relevant codebase files before asking questions to make them targeted
- ALWAYS include a Code References section listing files/lines you read during research
- ALWAYS add the feature to the icebox in `docs/features/master.md` after writing the brief
- Keep the interview conversational, not interrogative
- If the user's feature idea is vague, help them sharpen it through questions — don't assume
- If the user already provided detailed requirements (workflow, scope decisions, etc.), skip questions they've already answered and only ask about gaps
