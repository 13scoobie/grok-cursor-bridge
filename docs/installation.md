# Installation Guide

This guide is written for users of **any skill level** on macOS, Linux, and Windows.

`cursor-bridge` works the same on all platforms. The only differences are how you create folders and edit files.

---

## Step 1: Install the Skill

You have two options. Pick the one that feels easier.

### Option A: Manual Install (Fastest to Try)

This is the simplest way to get started.

1. Open your file manager or terminal and go to your Grok skills folder:
   - **macOS / Linux**: `~/.grok/skills/`
   - **Windows**: `%USERPROFILE%\.grok\skills\` (usually `C:\Users\YourName\.grok\skills\`)

2. Create a new folder called `cursor-bridge` inside it.

3. Download the skill file:
   - Go to this link in your browser:  
     `https://raw.githubusercontent.com/13scoobie/grok-cursor-bridge/main/cursor-bridge/SKILL.md`
   - Right-click → **Save As** → save it as `SKILL.md` inside the `cursor-bridge` folder you just created.

### Option B: Install as a Plugin (Easiest to Update Later)

If you already use plugins with Grok, run this command in the Grok TUI:

```bash
grok plugin install 13scoobie/grok-cursor-bridge
```

To update later, just run:
```bash
grok plugin update grok-cursor-bridge
```

---

## Step 2: (Recommended) Enable Better Integration

This step makes the imported skills show up nicely in `grok inspect` and work more automatically.

1. Open your Grok config file:
   - **macOS / Linux**: `~/.grok/config.toml`
   - **Windows**: `%USERPROFILE%\.grok\config.toml`

2. Add these lines at the bottom of the file:

```toml
[skills]
paths = ["~/.grok/cursor-imports"]
```

> **Windows users**: Use this instead:
> ```toml
> [skills]
> paths = ["%USERPROFILE%\\.grok\\cursor-imports"]
> ```

3. Save the file and **completely close and reopen the Grok TUI**.

---

## Step 3: Test It

Open any project folder that contains a `.cursor/` directory and run:

```
/cursor-bridge status
```

You should see a friendly response.

Then try:

```
/cursor-bridge import
```

---

## Common Questions

**Do I have to do Step 2?**  
No. The bridge works without it using the fallback mode. Step 2 just makes everything feel more native.

**What does "restart Grok" mean?**  
Fully quit the Grok application/TUI and open it again. Just running `/skills` is often enough, but a full restart is more reliable after config changes.

**I don't have `curl`. Can I still install it?**  
Yes — use Option A (Manual Install) above. Just download the file in your browser.

**I'm on Windows and the paths look weird**  
Use the Windows-style paths shown in the steps above. Grok handles both styles in most cases.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Skill doesn't appear | Fully restart Grok after installing |
| "No .cursor directory found" | Make sure your current working directory actually contains a `.cursor` folder |
| Wrappers don't show in `grok inspect` | You probably skipped Step 2 (the config change) |
| Want to remove everything the bridge created | Run `/cursor-bridge remove --all` |

---

## Next Steps

- Read the [How It Works](./how-it-works.md) guide (short and beginner-friendly)
- Try importing rules from one of your real projects
- Come back here if you run into any issues

If something is confusing, please open an issue on GitHub. This tool is meant to be easy for everyone.
