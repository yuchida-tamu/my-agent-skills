# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code plugin marketplace (`yuchida-agent-skills`) — a monorepo containing multiple plugins/skills that users install via `/plugin marketplace add yuchida-tamu/my-agent-skills`.

## Repository Structure

```
.claude-plugin/marketplace.json   # Marketplace registry — lists all plugins with metadata/versions
<plugin-name>/
  .claude-plugin/plugin.json      # Plugin manifest (name, version, description)
  skills/<skill-name>/SKILL.md    # Skill definition (trigger rules, behavior, output format)
  commands/<command>.md            # Slash commands (frontmatter: description, allowed-tools)
  README.md                       # User-facing plugin docs
```

Each plugin is a self-contained directory. The marketplace.json at the root ties them together.

## Current Plugins

- **tech-trend-digest** — Web-search-based tech news curation with bilingual EN/JP support. Has two commands (`/digest`, `/digest-preferences`), a skill, and persists user preferences + history to `memory/` files.
- **english-coach** — Passive skill (no commands) that auto-appends English corrections to every response. Skill-only plugin.

## Key Conventions

- **Plugin manifests** use `strict: true` in marketplace.json.
- **Skills** are defined in `SKILL.md` files with YAML frontmatter (`name`, `description`, `version`).
- **Commands** are `.md` files with YAML frontmatter (`description`, `allowed-tools`) that reference and delegate to their parent skill.
- **Bilingual support**: README.md contains both English and Japanese (日本語) sections. The tech-trend-digest plugin supports EN/JP/Both for digest output and EN/JP for chat language.
- **State files** for plugins that persist data go in `memory/` (e.g., `memory/tech-trend-digest-preferences.md`, `memory/tech-trend-digest-history.md`).

## Adding a New Plugin

1. Create `<plugin-name>/` directory with `.claude-plugin/plugin.json`, `README.md`, and `skills/<name>/SKILL.md`.
2. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array.
3. If the plugin has slash commands, add `commands/<command>.md` with appropriate `allowed-tools` in frontmatter.
4. External plugins (like `claude-office-visualizer`) use `"source": {"source": "npm", ...}` instead of a local path.
