---
description: Work on a sibling repo using your home repo's accumulated context
allowed-tools: Read, Glob, Agent, AskUserQuestion, Bash
---

Run the cross-repo skill to work on a sibling repository while compounding context from the current (home) repo.

Load and follow the instructions in the cross-repo skill. Execute all steps in order:
parse the user's natural language input to extract the sibling repo path and task description,
validate the path exists, gather home repo context (CLAUDE.md, memory files), spawn an agent
with the task and injected context, and report the results back to the user.

The user's input follows this command. Parse it as natural language — the repo path and task
can appear in any order.
