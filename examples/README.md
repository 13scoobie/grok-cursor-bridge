# Examples

This folder contains a small, realistic example so you can see exactly what `cursor-bridge` looks for and how it behaves.

## sample-project

This is a minimal fake project that includes:

- 1 agent (`.cursor/agents/my-implementer.md`)
- 1 skill (`.cursor/skills/my-architecture-guardian/SKILL.md`)
- A `.cursorrules` file at the root

### How to try it

1. Copy the entire `sample-project` folder somewhere on your computer.
2. Open that folder in the Grok TUI.
3. Run:

   ```bash
   /cursor-bridge status
   /cursor-bridge import
   ```

You’ll see exactly how the bridge detects and wraps Cursor content.

## web-project (Second Example)

A lightweight example showing a different tech stack (web/API focused).

Contains:
- `.cursor/skills/api-guardian/SKILL.md`

This proves the bridge is completely generic and works for any kind of project.

## Recommendation

Try the bridge on both examples to see how it behaves with different structures and naming.
