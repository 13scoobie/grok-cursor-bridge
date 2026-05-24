# How It Works

`cursor-bridge` is deliberately simple. It doesn't hack Grok or require any special privileges. It uses only features Grok already exposes to users.

## The Core Idea

Grok has two relevant extension points:

1. **The `[skills]` configuration** in `~/.grok/config.toml`
2. **Thin wrapper SKILL.md files**

When you add a directory to the `paths` list under `[skills]`, Grok will scan that directory for `SKILL.md` files at startup (just like it scans `~/.grok/skills/` and `~/.claude/skills/`).

The bridge simply generates well-formed `SKILL.md` files inside `~/.grok/cursor-imports/<your-project>/` based on what it finds in your project's `.cursor/` folder.

## What the Bridge Actually Does

When you run `/cursor-bridge import`:

1. **Detection**  
   It looks for a `.cursor/` directory relative to your current working directory or git root (using normal `find` and `git` commands).

2. **Inspection**  
   It reads the frontmatter of every `.cursor/skills/*/SKILL.md` and `.cursor/agents/*.md` file, plus any `.cursorrules` at the project root.

3. **Wrapper Generation**  
   For each discovered definition, it creates a small wrapper file that:
   - Preserves the original `name` and `description`
   - Adds extra trigger phrases so the skill is easy to invoke in Grok
   - Includes clear attribution ("This came from your Cursor rules in project X")
   - Points back to the real source file

4. **Result**  
   The next time Grok scans skills, your Cursor rules appear as normal skills.

## Why This Feels Native

Because the wrappers are real `SKILL.md` files in a location Grok is configured to watch, they get all the normal benefits:

- Show up in `grok inspect`
- Can be invoked with `/name`
- Participate in automatic skill selection based on description
- Can be used alongside any other skills (including the superpowers plugin)

## Comparison to Claude Support

Grok already does something very similar for Claude users:

- It has a hardcoded scan for `~/.claude/skills/`
- It understands `Claude.md` as a project rules file

`cursor-bridge` gives Cursor users the same kind of experience, but implemented as a user-level tool rather than a hardcoded feature.

## Safety & Design Principles

- The bridge **never writes** to your `.cursor/` directory or project source.
- All generated wrappers are clearly marked as derived.
- You stay in control — you can delete wrappers anytime with `/cursor-bridge remove`.
- Editing rules stays in Cursor (your normal workflow). The bridge is just a synchronization tool.

## Limitations (By Design)

- Changes in Cursor are not automatically reflected in Grok. You need to run `refresh` (or the skill can remind you).
- This is a bridge, not a two-way sync (though two-way sync could be added later if there's demand).

## Future Possibilities

Because everything is built on public extension points, the community can evolve this over time:

- Better automatic detection
- SessionStart hooks that suggest refreshing
- Support for more Cursor features
- A proper plugin with a small UI

The current implementation is intentionally minimal and robust.

---

**Next**: Go back to the [main README](../README.md) or check the [Installation Guide](./installation.md).
