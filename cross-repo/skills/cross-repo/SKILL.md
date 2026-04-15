---
name: cross-repo
description: >
  Work on a sibling repository while staying in your home repo, compounding accumulated
  context (CLAUDE.md, memory files, project knowledge) into work performed on other
  repositories. Use this skill when the user invokes /cross-repo with a target repo path
  and a task description. The skill parses the user's natural language input, gathers
  home repo context, and spawns an agent to execute the task on the sibling repo.
---

# Cross-Repo Skill

You enable users to work on sibling repositories without leaving their home repo. The home repo's accumulated knowledge (CLAUDE.md, memory files, conventions) is injected into an agent that operates on the target repo.

## Workflow

### Step 1: Parse Input

The user provides natural language that contains:
- **A sibling repo path** (e.g., `../other-repo`, `../my-app`, `/Users/someone/code/project`)
- **A task description** (e.g., "fix the login bug", "add error handling to the API routes")

Extract both from the user's message. The path can appear anywhere in the message.

If the repo path is missing or ambiguous, use AskUserQuestion to ask:
> "Which sibling repo should I work on? Provide a relative or absolute path (e.g., `../other-repo`)."

If the task description is missing, use AskUserQuestion to ask:
> "What task should I perform on the sibling repo?"

### Step 2: Validate Sibling Repo

Run a Bash command to verify the path exists:

```bash
test -d <repo-path> && echo "valid" || echo "invalid"
```

If invalid, respond with:
> "The path `<repo-path>` does not exist or is not a directory. Please provide a valid path to the sibling repo."

**Stop here if invalid. Do not proceed.**

Also resolve the path to an absolute path:

```bash
cd <repo-path> && pwd
```

### Step 3: Gather Home Repo Context

Collect context from the home repo (current working directory):

1. **Read CLAUDE.md** — if it exists at the project root
2. **Read all memory files** — Glob for `memory/**/*.md` and read each file
3. **Read MEMORY.md** — if it exists in the `.claude/` directory or project root

Concatenate all context into a single block to inject into the agent prompt.

### Step 4: Spawn Agent

Launch an Agent (subagent_type: "general-purpose") with a prompt structured as follows:

```
You are working on a repository at: <absolute-path-to-sibling-repo>

## Your Task
<task description from user>

## Context from Home Repository
The user is working from a home repository that has accumulated the following knowledge.
Use this context to inform your work — it contains conventions, preferences, project
knowledge, and institutional memory that should guide your approach.

### CLAUDE.md (Project Instructions)
<contents of CLAUDE.md, or "No CLAUDE.md found." if absent>

### Memory Files
<contents of each memory file, prefixed with its filename>

## Instructions
- Use absolute paths when reading or editing files (base: <absolute-path-to-sibling-repo>)
- First explore the sibling repo structure to understand it (use Glob, Grep, Read)
- Then execute the task
- After completing the task, provide a clear summary of:
  - What files were created, modified, or deleted
  - What changes were made and why
  - Any issues encountered or decisions made
```

### Step 5: Report Results

After the agent completes, relay its summary back to the user. Include:
- What the agent did
- Which files were changed
- Any issues or notes from the agent

## Rules

- **Never fabricate repo paths** — only use paths the user provides
- **Always validate the path** before proceeding
- **Always resolve to absolute path** to avoid ambiguity in the agent
- **Pass all home repo context** — the whole point is context compounding
- **The agent writes directly** — no approval gate, but always report what changed
- **One task at a time** — each invocation handles one task on one sibling repo
