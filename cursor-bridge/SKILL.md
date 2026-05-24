---
name: cursor-bridge
description: |
  Bridge any project's .cursor/agents/ and .cursor/skills/ into Grok as first-class skills, using the same discovery mechanism Grok already uses for ~/.claude/skills/.
  Use when the user mentions .cursor, cursor agents, cursor skills, or wants to import/refresh Cursor rules so they appear in grok inspect, /<name>, and automatic invocation.
  Also use proactively when working in a repo that has a .cursor directory.
metadata:
  short-description: "Turn any .cursor/ setup into native Grok skills via thin wrappers + [skills] paths"
---

# Cursor Bridge

Make any project's `.cursor/` definitions first-class citizens inside Grok — exactly like native skills or the existing Claude compatibility layer.

You detect `.cursor/` trees (at root or anywhere in the tree), generate clean attributed wrapper SKILL.md files, and keep them synchronized so Cursor skills and agents become discoverable, invocable, and auto-triggerable in Grok.

## Why This Exists

Many people maintain excellent project-specific agents and skills inside Cursor (in `.cursor/agents/` and `.cursor/skills/`). When they switch to Grok for deeper agentic work, that investment disappears. This skill closes the gap using only Grok's supported extension points (`[skills] paths` + thin wrappers), the same way Grok already pulls in `~/.claude/skills/`.

## Core Workflow

1. **Detect** `.cursor/` relative to the current workspace (git root preferred, then CWD). Works even if `.cursor/` is nested.
2. **Inspect** agents (`.cursor/agents/*.md`) and skills (`.cursor/skills/*/SKILL.md`) plus the optional `.cursorrules` file.
3. **Import / Refresh** → create or update thin, well-attributed wrapper SKILL.md files under a user-controlled imports directory.
4. The wrappers make everything **first-class**: visible in `grok inspect`, invocable as `/name`, automatically triggered by description matching, and fully composable with other skills and project rules.
5. Provide an excellent **fallback** (`load <name>`) that works immediately even before any config changes.

## Detection

Always use safe, read-only operations:
- `run_terminal_command` with relative paths: `ls`, `git rev-parse --show-toplevel`, `find . -path '*/.cursor/*' ...`
- Graceful handling when no `.cursor/` exists.
- Read frontmatter (and optionally body) of discovered files with `read_file`.

Never write to or modify anything inside any `.cursor/` directory.

## Wrapper Generation & Naming (Designed for Multiple Projects)

Recommended configuration (user adds once):

```toml
[skills]
paths = ["~/.grok/cursor-imports"]
```

Wrapper layout (example):
```
~/.grok/cursor-imports/
  my-cool-app/
    architecture-guardian/
      SKILL.md
    swiftui-pro/
      SKILL.md
    mission-commander/
      SKILL.md
  another-project/
    ...
```

Naming strategy (in priority order):
1. Clean slug derived from `git remote get-url origin` when available.
2. Current directory or git root basename.
3. User override via `import --as <group-name>`.

Each wrapper must contain:
- Preserved original `name` and `description` (with added trigger phrases).
- Clear **Source** attribution line pointing back to the exact `.cursor/...` path.
- Timestamp of last import/refresh.
- Strong encouragement to edit the source in Cursor and refresh here.

Example neutral wrapper header:

```markdown
---
name: architecture-guardian
description: |
  [original Cursor description]
  Imported from .cursor/skills/architecture-guardian/SKILL.md in ~/projects/my-cool-app.
  Grok users: also triggered by "architecture guardian from cursor", "cursor clean architecture".
---
# architecture-guardian (imported from Cursor)

**Source**: `.cursor/skills/architecture-guardian/SKILL.md` (last refreshed <timestamp>)

> The authoritative version lives in the project's .cursor/ directory. Edit there (usually in Cursor), then run `/cursor-bridge refresh`.

[full or key sections of original body]
```

## Commands

- `status` / `detect` — Show what `.cursor/` content exists in the current workspace and what would be imported.
- `import` (or `refresh`) — Generate/update wrappers. Supports `--as <group-name>` for custom naming and smart grouping of agents vs skills.
- `list-imported` — Show all currently bridged wrappers across projects with source info.
- `remove <group>` or `remove <name>` — Clean up specific groups or individual wrappers.
- `load <name>` — Immediate fallback: read the live `.cursor/` file right now and inject its rules (no wrappers or config change required).

After any import/refresh action, always clearly state:
- What was created/updated
- Exact paths
- How to verify (`grok inspect`, `/<name>`, new shell, etc.)

## .cursorrules Support

When a `.cursorrules` (JSON) file exists at the `.cursor/` level:
- Surface the `customCommands` mappings (e.g. "plan" → specific agent).
- Note any `routing` or model preferences.
- Optionally suggest loading the referenced agents/skills together.

This is extremely useful context that many Cursor power users rely on.

## Using This Skill With Your Own Projects

You can use this with **any** project that has a `.cursor/` folder — it doesn't matter what language or framework you use.

**Recommended flow:**

1. Go into your project folder.
2. Run `/cursor-bridge status` to see what it finds.
3. Run `/cursor-bridge import` (or `/cursor-bridge import --as my-project-name`).
4. (Optional but nice) Add the one-line config change shown above so everything feels native.
5. From now on, just run `/cursor-bridge refresh` whenever you update your rules in Cursor.

**Don't want to edit your config?**  
That's fine. You can always use the fallback command:

```bash
/cursor-bridge load my-skill-name
```

This works immediately with no setup.

The goal is to make your Cursor investment useful in Grok with as little friction as possible.

## Integration & Safety (Non-Negotiable)

- Full composability with `using-superpowers` and all other skills.
- Strict relative paths for all workspace operations.
- Zero modification of any `.cursor/` content or project source.
- Every wrapper and injected block must clearly attribute its Cursor origin.
- `refresh` is always safe and idempotent.
- Excellent fallback behavior when the user has not yet configured extra skill paths.

## Self-Verification After Import

After generating wrappers, confirm:
- Files exist on disk at the expected locations.
- At least one wrapper can be read back and its core rule summarized.
- The user knows the next verification steps (`grok inspect`, direct `/name` invocation, fresh session).

This skill should feel polished and trustworthy the first time a stranger uses it.

## When Not to Use

- The user is deep inside a strong specialized skill (e.g. systematic-debugging) unless they explicitly want to combine them.
- Purely exploratory chat with no Cursor rules relevant.

Keep the skill body focused, actionable, and welcoming to users coming from any tech stack or Cursor workflow. The goal is to make other people's Cursor investment portable into Grok without forcing them to duplicate content.
