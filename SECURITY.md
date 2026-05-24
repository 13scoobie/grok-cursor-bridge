# Security

## Overview

`grok-cursor-bridge` is a tool that reads files from your local `.cursor/` directories and writes small wrapper files to `~/.grok/cursor-imports/`.

## What it does

- Reads `.cursor/agents/*.md` and `.cursor/skills/*/SKILL.md` files (read-only on your Cursor rules)
- Optionally reads `.cursorrules` at the project root
- Creates small, clearly attributed `SKILL.md` wrapper files in your local Grok configuration directory
- Never modifies your original Cursor files or project source code

## What it does NOT do

- Does not send your rules or project data anywhere
- Does not execute arbitrary code from your Cursor rules
- Does not require network access after installation
- Does not modify your `~/.grok/config.toml` (you must do this manually)

## Trust & Review

This is an early, open-source project. You are encouraged to review the source code before installing, especially the `skills/cursor-bridge/SKILL.md` file.

The plugin runs with the same permissions as your Grok TUI session.

## Reporting Issues

If you discover a security issue, please open a private security advisory on GitHub or contact the maintainer directly.

## Updates

Security-related changes will be noted in the [CHANGELOG.md](CHANGELOG.md).
