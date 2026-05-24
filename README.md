# grok-cursor-bridge

**Use your Cursor rules inside Grok — with almost no effort.**

If you’ve spent time building great agents and skills in Cursor, this tool lets you use them when working in the Grok Build TUI.

No duplication. No context switching. Just better results.

---

## The Problem It Solves

You do great work in Cursor defining how you want things built.

Then you open the same project in Grok… and it has no idea about any of your rules.

This bridge fixes that.

---

## Quick Start (Most People)

### 1. Install the skill (choose one)

**Recommended – As a plugin:**
```bash
grok plugin install 13scoobie/grok-cursor-bridge --trust
```

**Alternative – Manual copy:**
- Download: https://raw.githubusercontent.com/13scoobie/grok-cursor-bridge/main/skills/cursor-bridge/SKILL.md
- Save it to:
  - macOS/Linux: `~/.grok/skills/cursor-bridge/SKILL.md`
  - Windows: `%USERPROFILE%\.grok\skills\cursor-bridge\SKILL.md`

### 2. (Strongly recommended) Add this to your config

Add the following to your `~/.grok/config.toml`, then fully restart Grok:

```toml
[skills]
paths = ["~/.grok/cursor-imports"]
```

> Windows users: You can also use `paths = ["%USERPROFILE%\\.grok\\cursor-imports"]`

### 3. Use it

Go into any project that has a `.cursor/` folder and run:

```bash
/cursor-bridge import
```

Your Cursor rules are now available inside Grok.

For full step-by-step instructions (including Windows), see the [Installation Guide](docs/installation.md).

---

## What You Get

After importing, you can:

- Call your rules directly: `/architecture-guardian`, `/my-implementer`, etc.
- Let Grok automatically use the right rules based on what you ask
- See them in `grok inspect`
- Use them alongside any other skills you already have

Your actual rules stay in Cursor. You just tell the bridge to refresh when you make changes.

---

## Documentation

| I want to...                    | Read this |
|--------------------------------|-----------|
| Install it step-by-step (any skill level) | [Installation Guide](docs/installation.md) |
| Understand how it actually works | [How It Works](docs/how-it-works.md) |
| See a working example           | [examples/](examples/) |

---

## Key Benefits

- Works with **any** Cursor project
- Supports both agents and skills
- Understands your `.cursorrules`
- Completely safe — never touches your original files
- Works great even if you skip the config step (has a good fallback)
- Plays nicely with the superpowers plugin and everything else

---

## Example

```bash
# In a project with good Cursor rules
/cursor-bridge import --as my-app

# Later in the same session...
/my-architecture-guardian
```

Grok will now follow those rules.

---

## License & Attribution

This project is MIT licensed **with one extra rule**:

If you use or share the code, please credit the original repository:

→ https://github.com/13scoobie/grok-cursor-bridge

See [LICENSE](./LICENSE) for details.

---

**Not affiliated with xAI or Cursor.**  
Just a useful community tool.

---

**Ready to try it?**

Pick any project where you actually have decent Cursor rules and run:

```bash
/cursor-bridge status
```

It takes 30 seconds. You’ll immediately see whether it’s useful for how *you* work.
