# Installation Guide

This guide is written for users of **any skill level** on macOS, Linux, and Windows.

`cursor-bridge` works the same on all platforms. The only differences are how you create folders and edit files.

---

## Step 1: Install the Skill

You now have two good options. The **plugin method** is recommended.

### Option 1: Install as a Plugin (Recommended)

```bash
grok plugin install 13scoobie/grok-cursor-bridge --trust
```

To update later:
```bash
grok plugin update grok-cursor-bridge
```

### Option 2: Manual Install

1. Go to your Grok skills folder:
   - **macOS / Linux**: `~/.grok/skills/`
   - **Windows**: `%USERPROFILE%\.grok\skills\`

2. Create a folder called `cursor-bridge` inside it.

3. Download the skill file:

   **Browser (easiest):**
   - Visit:  
     https://raw.githubusercontent.com/13scoobie/grok-cursor-bridge/main/skills/cursor-bridge/SKILL.md
   - Save the file as `SKILL.md` inside the `cursor-bridge` folder.

   **Terminal (macOS / Linux):**
   ```bash
   mkdir -p ~/.grok/skills/cursor-bridge
   curl -o ~/.grok/skills/cursor-bridge/SKILL.md \
     https://raw.githubusercontent.com/13scoobie/grok-cursor-bridge/main/skills/cursor-bridge/SKILL.md
   ```

   **Terminal (Windows PowerShell):**
   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.grok\skills\cursor-bridge"
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/13scoobie/grok-cursor-bridge/main/skills/cursor-bridge/SKILL.md" -OutFile "$env:USERPROFILE\.grok\skills\cursor-bridge\SKILL.md"
   ```

After installing, fully restart the Grok TUI (or run `/skills`).

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
Yes — use the browser download method in the Manual Install section. Just save the raw file.

**I'm on Windows and the paths look weird**  
Use the Windows-style paths shown in the steps above. Grok handles both styles in most cases.

**Does this work equally well on macOS, Linux, and Windows?**  
Yes. The core functionality is the same. The main differences are file paths and how you create folders. The documentation includes instructions for all three.

**Recommended testing approach:**
- Test the plugin install method on your main machine.
- Test the manual copy method on at least one other OS if possible (especially Windows if you're primarily on macOS/Linux).
- Pay attention to:
  - Correct creation of the `cursor-bridge` folder
  - Proper path in `config.toml`
  - Whether `grok inspect` correctly shows the imported skills after restart

If you run into platform-specific issues, please open a GitHub issue with your OS and exact error.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Skill doesn't appear | Fully restart Grok after installing |
| "No .cursor directory found" | Make sure your current working directory actually contains a `.cursor` folder |
| Wrappers don't show in `grok inspect` | You probably skipped Step 2 (the config change) |
| Want to remove everything the bridge created | Run `/cursor-bridge remove --all` |

---

## Updating the Skill

**If you installed via plugin (recommended):**
```bash
grok plugin update grok-cursor-bridge
```

**If you installed manually:**
1. Re-download the latest `SKILL.md` from the link in Step 1.
2. Replace the file in your `cursor-bridge` folder.
3. Restart Grok or run `/skills`.

---

## Next Steps

- Read the [How It Works](./how-it-works.md) guide (short and beginner-friendly)
- Try importing rules from one of your real projects
- Come back here if you run into any issues

If something is confusing, please open an issue on GitHub. This tool is meant to be easy for everyone.
