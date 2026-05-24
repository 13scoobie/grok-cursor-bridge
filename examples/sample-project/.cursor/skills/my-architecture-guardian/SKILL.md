---
name: my-architecture-guardian
description: Enforces clean architecture and layer separation for this project. Use when reviewing structure, adding new features, or refactoring.
---

# My Architecture Guardian

Enforce these principles:

- Separate concerns (Domain, Data, Presentation)
- Keep views simple
- Use dependency injection and protocols
- New business logic belongs in the core module, not in UI code

## Review Checklist

- [ ] No business logic in presentation layer
- [ ] Dependencies point inward
- [ ] Proper abstractions via protocols where needed
