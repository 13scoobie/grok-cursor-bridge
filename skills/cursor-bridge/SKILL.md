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

Always use safe, read-only operations with clear, user-friendly output:

- Use `run_terminal_command` with relative paths: `ls -la .cursor 2>/dev/null`, `git rev-parse --show-toplevel 2>/dev/null`, `find . -path '*/.cursor/*' -type f 2>/dev/null | sort`
- **Always** check the results and explain the situation to the user in plain language.

### Error Handling Rules (Critical)

Handle these situations gracefully and explain them clearly:

- **No `.cursor/` directory found**:
  - Tell the user politely: "No .cursor/ directory detected in the current workspace."
  - Suggest they `cd` into the correct project or run from a folder that contains Cursor rules.
  - Offer to show what a typical structure looks like (point to the `examples/` folder in the repo).

- **Not inside a git repository**:
  - Still allow the command to work.
  - Use the current directory basename as the group name (or ask via `--as`).
  - Mention: "Not a git repo — using current folder name for grouping. Git is recommended for cleaner project names."

- **Inside a git worktree**:
  - Prefer the main worktree root when possible for stable naming.
  - Fall back to the current directory if detection is ambiguous.

- **`.cursor/` exists but is empty or has no valid agents/skills**:
  - Clearly report: "Found .cursor/ but no agents or skills with valid frontmatter."
  - List exactly what was discovered (even if zero).
  - Suggest they may need to create rules first.

- **Permission errors when writing wrappers**:
  - Explain the exact path that failed.
  - Suggest: "Check write permissions for ~/.grok/cursor-imports/ or run with appropriate rights."

- **Malformed or missing frontmatter** in source files:
  - Skip the bad file gracefully.
  - Report: "Skipped file X — missing or invalid frontmatter (name/description required)."
  - Continue processing the rest of the files.

- **Multiple `.cursor/` directories** in the tree:
  - Prefer the one closest to the git root (or current working directory).
  - Clearly state: "Multiple .cursor/ directories found. Using the one at: <path>"

Never write to or modify anything inside any `.cursor/` directory.

### Platform Notes
- Prefer relative paths and standard commands that work across macOS, Linux, and Windows (when run through the Grok TUI).
- When suggesting paths to the user, provide both Unix-style (`~/.grok/...`) and Windows-style (`%USERPROFILE%\.grok\...`) versions when relevant.
- Git commands generally work the same across platforms inside the TUI.

## Ignore List Support

When the user passes `--ignore name1,name2` (or similar):
- Parse the comma-separated list of names to skip.
- During detection and import, completely skip any agent or skill whose `name` matches an ignored value (case-insensitive).
- Clearly report in the output which items were skipped due to the ignore list.
- This is especially useful for large projects with many Cursor rules.

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

### Large File Handling (Excerpt Mode)

If a source `.cursor/skills/*/SKILL.md` or agent file is very long (roughly > 150 lines or very large content):
- Do **not** inline the entire content into the wrapper.
- Instead, use an excerpt approach:
  - Include the frontmatter + first 30-50 lines of the body.
  - Add a clear note: "This is an excerpt. The full original file is at: [path]"
- This keeps wrappers manageable while still giving Grok the important rules.
- Always prefer full content when the source is reasonably sized.

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

**`status` / `detect`** (Most important for user experience)
- Always run detection first using the rules above.
- Produce a clear, structured report even when things go wrong. Structure your response like this when possible:

  **Detection Results**
  - Workspace: ...
  - `.cursor/` found: Yes/No + location
  - Agents found: X
  - Skills found: Y
  - .cursorrules present: Yes/No
  - Proposed import group: "xxx"
  - Issues/Warnings: (list any edge cases from the Error Handling Rules)

- If nothing useful is found, be helpful: suggest running from the correct directory or point them to the examples.
- Always end with recommended next command(s).

**`import` (or `refresh`)**
- Support `--as <group-name>` for custom naming.
- Support `--ignore name1,name2` to skip specific agents or skills during import (very useful for large projects).
- Be smart about grouping (agents vs skills).
- On success: Show exactly what was created/updated with full paths.
- On partial success: Clearly list what succeeded and what failed (with reasons).
- Always remind the user about the recommended config change if they haven't done it yet.

**`list-imported`**
- Show a clean summary of everything the user has previously imported across all projects.
- Include source paths and last import timestamps.

**`remove <group>` or `remove <name>`**
- Make it safe — ask for confirmation on broad removals if possible.
- Clearly report what was deleted.

**`load <name>`** (Fallback)
- This is the safety net. It should always work, even with zero config.
- Read the live source file and inject the rules with clear attribution.

**General Rule for All Commands:**
After every action (especially import/refresh), clearly state:
- What happened
- Exact file paths affected
- Recommended next verification steps (`grok inspect`, trying `/name`, new session, etc.)
- Any warnings or suggested improvements

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
