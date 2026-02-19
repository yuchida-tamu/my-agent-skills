---
description: Update your tech digest language, chat language, and topic preferences
allowed-tools: Read, Write, Edit, AskUserQuestion
---

Update the user's tech trend digest preferences.

1. Read the current preferences from `memory/tech-trend-digest-preferences.md` and show them.
2. Ask the user what they want to change using AskUserQuestion — digest language, chat language,
   topics, or any combination.
3. Apply the changes and overwrite the preferences file.
4. Confirm the updated preferences (using the user's chat language preference).

If the user provided specifics in their message (e.g., "add Security", "switch to Japanese",
or "chat in English"), apply those directly without re-asking.
