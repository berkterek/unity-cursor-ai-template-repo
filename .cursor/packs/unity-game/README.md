# Unity Game Pack

Reusable Unity-specific guidance for this Cursor workflow template.

This pack should stay small. It contains guidance that is broadly useful for
most Unity projects, not full gameplay implementations or genre recipes.

## Included Guides

| Guide | Purpose |
|-------|---------|
| `agents/unity-setup.md` | Unity Editor, scene, prefab, asset, package, and runtime setup guidance. |
| `guides/unity-mcp.md` | Unity MCP usage rules and verification loop. |
| `guides/input-system.md` | New Input System default guidance. |
| `guides/serialization-safety.md` | Unity serialized data safety rules. |

## Use This Pack For

- Unity Editor setup.
- Scene, prefab, and asset workflows.
- ScriptableObject/config guidance.
- Unity MCP workflow.
- New Input System defaults.
- Unity serialization safety.
- Unity package and test runner notes.
- Unity-specific validation and smoke checks.

## What Does Not Belong Here

- One project's exact scene names, folder names, or local architecture
  decisions.
- Full genre implementations such as card games, RPGs, match-3, or tower
  defense.
- Third-party package rules unless the template requires that package by
  default.
- Long code-heavy references that should be optional per project.

Do not put one project's exact scene names, folder names, or local architecture
decisions here unless they are meant to be reused across Unity projects.

Project-specific Unity details belong in:

- `.cursor/project/PROJECT.md`
- `.cursor/project/STRUCTURE.md`
- `.cursor/project/TOOLING.md`
- `.cursor/project/RULES.md`
- `.cursor/project/CODING_CONVENTIONS.md`
