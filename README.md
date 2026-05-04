# Unity Cursor AI Template

Reusable `.cursor` workflow template for Unity projects. The template separates
Cursor Project Rules from reusable workflow guidance, project-specific overlays,
and Unity-specific setup instructions.

## What This Is

This repository is a starting point for AI-assisted Unity project work with
Cursor. It provides:

- Cursor Project Rules in `.mdc` format.
- Core agent role templates.
- Core workflow command templates.
- Progress, event, mailbox, heartbeat, and checkpoint protocols.
- Project overlay files that each Unity project fills in.
- A Unity pack for Unity MCP, New Input System, serialization safety,
  architecture (VContainer/IEventBus), C# coding conventions, performance,
  ECS/DOTS, Addressables, and guardrail/setup rules.
- Coding, game design, and technical design templates.

## Folder Layout

```text
.cursor/
├── README.md
├── rules/
│   ├── 000-template-entry.mdc
│   ├── 010-project-overlay.mdc
│   ├── 020-workflow-execution.mdc
│   ├── 030-review-quality.mdc
│   ├── 040-unity-pack.mdc
│   ├── 050-implementation.mdc
│   ├── 060-testing.mdc
│   ├── 070-commit.mdc
│   ├── 080-architecture.mdc
│   ├── 090-performance.mdc
│   ├── 100-ecs-dots.mdc
│   └── 110-addressables.mdc
├── core/
│   ├── agents/
│   ├── commands/
│   └── protocols/
├── project/
│   ├── PROJECT.md
│   ├── STRUCTURE.md
│   ├── WORKFLOW.md
│   ├── TOOLING.md
│   ├── RULES.md
│   ├── CODING_CONVENTIONS.md
│   ├── LEARNED.md
│   └── PROGRESS.md
├── templates/
│   ├── CODING_CONVENTIONS.md
│   ├── GDD_TEMPLATE.md
│   └── TDD_TEMPLATE.md
├── packs/
│   └── unity-game/
│       ├── README.md
│       ├── agents/
│       │   └── unity-setup.md
│       └── guides/
│           ├── guardrails.md
│           ├── unity-mcp.md
│           ├── input-system.md
│           ├── serialization-safety.md
│           ├── architecture.md
│           ├── csharp-coding.md
│           ├── performance.md
│           ├── ecs-dots.md
│           └── addressables.md
└── manifests/
```

## Core Idea

- `rules/` contains Cursor-readable Project Rules.
- `core/` contains reusable detailed workflow behavior.
- `project/` is filled per repository.
- `packs/` contains technology-specific guidance, such as Unity.
- `templates/` contains reusable fill-in documents such as coding
  conventions, GDD, and TDD.
- `manifests/` records template decisions.

Do not put project-specific coding style into `rules/` or `core/`. Put it in
`.cursor/project/CODING_CONVENTIONS.md`.

Do not put project-specific folder or module structure into `rules/` or `core/`.
Put it in `.cursor/project/STRUCTURE.md`.

Do not put full gameplay, genre, platform, engine-system, or third-party package
references into the base template. Keep those as optional per-project packs.

Use `.cursor/project/LEARNED.md` for repeated project-specific patterns
discovered over time. Do not use it for generic Unity advice or temporary notes.

## Unity Pack

The included Unity pack is intentionally small and reusable:

```text
.cursor/packs/unity-game/
```

It includes:

- `agents/unity-setup.md`: Unity Editor, scene, prefab, asset, package, and
  runtime setup guidance.
- `guides/guardrails.md`: high-risk Unity safety checklist for serialized
  files, editor/runtime separation, input boundaries, scene ownership, and asset
  references.
- `guides/unity-mcp.md`: Unity MCP workflow, batching, console checks, and MCP
  vs file-edit rules.
- `guides/input-system.md`: New Input System as the default input approach for
  new Unity projects.
- `guides/serialization-safety.md`: serialized field rename, prefab, asset, and
  ScriptableObject safety rules.
- `guides/architecture.md`: VContainer DI, 5-file module structure, IEventBus
  pattern, MonoBehaviour adapter pattern, no-singleton rule.
- `guides/csharp-coding.md`: naming conventions, namespace policy, `#region`
  order, UniTask/async rules, null check rules (Unity vs plain C#).
- `guides/performance.md`: zero-alloc hot paths, caching, MaterialPropertyBlock,
  UI Canvas splitting by update frequency, object pooling.
- `guides/ecs-dots.md`: Authoring/Baker pattern, component naming, ISystem +
  IJobEntity, EntityCommandBuffer, hybrid OOP-ECS linking.
- `guides/addressables.md`: no `Resources.Load`, UniTask async loading, handle
  lifecycle, centralized address constants.

The base template does not include large genre or package-specific references.
Those should be added only when a project needs them.

## First-Time Use In A Unity Project

1. Copy the `.cursor/` folder into your Unity project root.
2. Open `.cursor/README.md`.
3. Fill `.cursor/project/PROJECT.md`.
4. Fill `.cursor/project/STRUCTURE.md`.
5. Fill `.cursor/project/TOOLING.md`.
6. Fill `.cursor/project/CODING_CONVENTIONS.md`.
7. Fill `.cursor/project/RULES.md`.
8. Use `.cursor/templates/GDD_TEMPLATE.md` when the game or feature design
   needs clarification.
9. Use `.cursor/templates/TDD_TEMPLATE.md` when the technical design needs to
   be planned before implementation.
10. Fill `.cursor/project/LEARNED.md` over time with repeated project-specific
    patterns.
11. Create a phase/task plan in `.cursor/project/WORKFLOW.md`.
12. Ask Cursor to dry-run, validate, or execute the workflow.

## Typical Cursor Prompts

Readiness check:

```text
Read .cursor/README.md, .cursor/rules/*.mdc, and .cursor/project/*.md.
Tell me what is missing before this Unity project is ready for workflow
execution.
```

Dry run:

```text
Use .cursor/core/commands/dry-run.md and preview the workflow in
.cursor/project/WORKFLOW.md. Do not modify files.
```

Execute a phase:

```text
Use .cursor/core/commands/orchestrate.md and execute Phase 1 from
.cursor/project/WORKFLOW.md. Follow Cursor rules, project overlays, and the
Unity pack.
```

Resume work:

```text
Use .cursor/core/commands/continue.md and resume from
.cursor/project/EVENTS.jsonl plus .cursor/runtime checkpoints.
```

Report status:

```text
Use .cursor/core/commands/status.md and report the current workflow state.
```

## Important Files

| File | Purpose |
|------|---------|
| `.cursor/rules/*.mdc` | Cursor Project Rules (000–110). |
| `.cursor/core/agents/` | Reusable role templates for implementation, tests, review, and commits. |
| `.cursor/core/commands/` | Reusable workflow commands such as dry run, orchestrate, continue, status, stop, and validate. |
| `.cursor/core/protocols/` | Reusable runtime state formats. |
| `.cursor/project/PROJECT.md` | Project identity, goals, enabled packs. |
| `.cursor/project/STRUCTURE.md` | Folder layout, modules, ownership, generated files. |
| `.cursor/project/TOOLING.md` | Build, test, lint, format, and Unity commands. |
| `.cursor/project/RULES.md` | Repository-specific hard and soft rules. |
| `.cursor/project/CODING_CONVENTIONS.md` | Concrete coding style for the project. |
| `.cursor/project/LEARNED.md` | Repeated project-specific patterns discovered during work. |
| `.cursor/project/WORKFLOW.md` | Phase/task execution plan. |
| `.cursor/project/PROGRESS.md` | Human-readable workflow status. |
| `.cursor/templates/GDD_TEMPLATE.md` | Game design document template. |
| `.cursor/templates/TDD_TEMPLATE.md` | Technical design document template. |
| `.cursor/packs/unity-game/` | Unity-specific reusable guidance. |
| `.cursor/packs/unity-game/README.md` | Unity pack overview. |
| `.cursor/packs/unity-game/guides/guardrails.md` | Unity high-risk change checklist. |
| `.cursor/packs/unity-game/guides/unity-mcp.md` | Unity MCP workflow and verification loop. |
| `.cursor/packs/unity-game/guides/input-system.md` | New Input System default guidance. |
| `.cursor/packs/unity-game/guides/serialization-safety.md` | Unity serialized data safety rules. |
| `.cursor/packs/unity-game/guides/architecture.md` | VContainer DI, module structure, IEventBus, MonoBehaviour adapter. |
| `.cursor/packs/unity-game/guides/csharp-coding.md` | Naming, namespace, async, null checks, #region order. |
| `.cursor/packs/unity-game/guides/performance.md` | Zero-alloc hot paths, caching, MaterialPropertyBlock, UI Canvas. |
| `.cursor/packs/unity-game/guides/ecs-dots.md` | Authoring/Baker, component naming, ISystem, ECB, hybrid linking. |
| `.cursor/packs/unity-game/guides/addressables.md` | No Resources.Load, handle lifecycle, UniTask async loading. |

## Recommended `.gitignore`

If you use this template inside a project, ignore runtime state:

```gitignore
.DS_Store
.cursor/runtime/
.cursor/project/EVENTS.jsonl
```

`PROGRESS.md` can stay committed as the initial template state.

## Status

This is a template repo. It is intended to be copied into Unity projects and
customized through `.cursor/project/`.
