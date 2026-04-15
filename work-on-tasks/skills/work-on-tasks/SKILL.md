---
name: work-on-tasks
description: Review GitHub project issues, select the next tasks based on priority and dependencies, spawn coding agents to execute them in parallel where possible, and create PRs that trigger automated code review.
version: 0.1.0
---

# Task Orchestrator

Review the GitHub project board, identify the highest-priority unblocked tasks, and execute them by spawning coding agents.

## When to use

- When the user says "/work-on-tasks" or asks to "work on the next tasks"
- When the user asks to "pick up work" or "continue building"

## Workflow

### Step 1: Assess project state

Run these commands to understand the current state:

```bash
# Get all open issues with labels and milestones
gh issue list --repo yuchida-tamu/basketball-sim-game --state open --json number,title,labels,milestone,body --limit 100

# Check what's currently in progress (open PRs)
gh pr list --repo yuchida-tamu/basketball-sim-game --state open --json number,title,headRefName

# Check recent closed issues to understand what's done
gh issue list --repo yuchida-tamu/basketball-sim-game --state closed --json number,title --limit 20
```

### Step 2: Select tasks

Apply these rules in order:

1. **Filter to current milestone.** Work on the earliest incomplete milestone first (M1 before M2, etc.).
2. **Check dependencies.** Read each issue's body for "Depends on: #X" lines. A task is **blocked** if any dependency issue is still open. Skip blocked tasks.
3. **Sort by priority.** Among unblocked tasks in the current milestone, pick by priority: P0 > P1 > P2 > P3.
4. **Identify parallelizable tasks.** Two tasks can run in parallel if:
   - Neither depends on the other
   - They don't modify the same files (check the issue descriptions and file manifests)
   - They are both unblocked
5. **Cap concurrency.** Run at most 3 agents in parallel to avoid overwhelming the system.

### Step 3: Gather context for each task

Before spawning an agent, prepare its context package by reading:

- The issue body (requirements, acceptance criteria, PRD reference)
- `CLAUDE.md` (conventions, commands, workflow rules)
- `docs/PRD.md` — the specific section referenced in the issue
- `docs/glossary.md` — terminology
- `docs/adr/` — all ADRs (agents must comply)
- `.memory/` — any existing specs or plans relevant to the task
- The current codebase files that the task will modify or depend on

### Step 4: Spawn coding agents

For each selected task, spawn an Agent with:
- `subagent_type: "expert-programmer"`
- `isolation: "worktree"` (each agent works on an isolated copy)
- A comprehensive prompt that includes:
  1. **The task:** what to build, acceptance criteria from the issue
  2. **Project rules:** key conventions from CLAUDE.md (no barrel exports, type over interface, semantic keys, Biome formatting, etc.)
  3. **Architecture constraints:** relevant ADRs
  4. **Terminology:** key glossary terms relevant to the task
  5. **PRD spec:** the exact PRD section content for the feature
  6. **Existing code context:** which files to read, what already exists
  7. **Testing requirements:** write unit tests, ensure `npm run check` passes
  8. **Branch naming:** `feature/{issue-number}-{short-description}`
  9. **Commit message:** reference the issue number (e.g., "Implement seeded RNG system (#8)")

If tasks are independent, spawn multiple agents in a **single message** (parallel tool calls).

If tasks are sequential (one depends on the other), spawn them one at a time, waiting for the previous to complete.

### Step 5: Create PRs and trigger review

After each agent completes and returns its worktree branch:

1. Push the branch to origin
2. Create a PR via `gh pr create` with:
   - Title referencing the issue: e.g., "Implement seeded RNG system (#8)"
   - Body with summary, test plan, and closing keyword: `Closes #8`
   - Labels matching the issue labels
   - Milestone matching the issue milestone

PR body template:
```markdown
## Summary
{what was implemented}

## Test plan
{test scenarios covered}

Closes #{issue_number}

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

3. **Trigger code review** by posting a `@claude` comment with the review prompt on the PR. This triggers the `claude.yml` GitHub Action which responds to `@claude` mentions.

Use this command for each PR:
```bash
gh pr comment {pr_number} --repo yuchida-tamu/basketball-sim-game --body '@claude Please review this PR.

In addition to standard code-level review (correctness, style, bugs, security), perform the following project-level checks:

1. **ADR compliance** — Read docs/adr/ and verify the implementation does not violate any accepted ADR.
2. **Glossary consistency** — Read docs/glossary.md and verify correct ubiquitous language usage.
3. **PRD alignment** — Read docs/PRD.md and verify the implementation matches the spec.
4. **Documentation completeness** — Flag if new ADRs, feature docs, glossary updates, or CLAUDE.md changes are needed.
5. **Convention compliance** — Read CLAUDE.md and verify: no barrel exports, types not interfaces, semantic keys, no hardcoded UI strings.

Report findings in clearly labeled sections.'
```

### Step 6: Report to user

After all agents complete and PRs are created, present a summary:

- Which issues were worked on
- PR links for each
- Any issues that were skipped (blocked, unclear requirements)
- What the next unblocked tasks will be after these merge

## Rules

- **Never work on blocked tasks.** If all tasks in the current milestone are blocked, report this to the user and suggest unblocking actions.
- **Always verify `npm run check` passes** before creating a PR. If an agent's work fails checks, diagnose and fix before pushing.
- **Respect the Development Workflow** in CLAUDE.md: branch → PR → review → merge. Never push to main.
- **Each agent gets one issue.** Don't combine multiple issues into one agent — it makes review harder and risks merge conflicts.
- **Update issue status.** After creating a PR for an issue, the PR's "Closes #N" will auto-close it on merge. No manual status update needed.
- **If a task requires design decisions not covered by the PRD or ADRs**, don't guess. Report it to the user and suggest running `/grill-me` or `/plan-feature` first.
- **Agents must run `npm run check` before considering their work done.** This runs typecheck + Biome lint + Jest tests.
