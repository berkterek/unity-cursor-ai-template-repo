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
- A small Unity pack for Unity MCP, New Input System, serialization safety, and
  setup rules.
- Coding convention templates.

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
│   └── 070-commit.mdc
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
│   └── PROGRESS.md
├── templates/
│   └── CODING_CONVENTIONS.md
├── packs/
│   └── unity-game/
│       ├── README.md
│       ├── agents/
│       │   └── unity-setup.md
│       └── guides/
│           ├── unity-mcp.md
│           ├── input-system.md
│           └── serialization-safety.md
└── manifests/
```

## Core Idea

- `rules/` contains Cursor-readable Project Rules.
- `core/` contains reusable detailed workflow behavior.
- `project/` is filled per repository.
- `packs/` contains technology-specific guidance, such as Unity.
- `templates/` contains reusable fill-in documents.
- `manifests/` records template decisions.

Do not put project-specific coding style into `rules/` or `core/`. Put it in
`.cursor/project/CODING_CONVENTIONS.md`.

Do not put project-specific folder or module structure into `rules/` or `core/`.
Put it in `.cursor/project/STRUCTURE.md`.

Do not put full gameplay, genre, platform, engine-system, or third-party package
references into the base template. Keep those as optional per-project packs.

## Unity Pack

The included Unity pack is intentionally small and reusable:

```text
.cursor/packs/unity-game/
```

It includes:

- `agents/unity-setup.md`: Unity Editor, scene, prefab, asset, package, and
  runtime setup guidance.
- `guides/unity-mcp.md`: Unity MCP workflow, batching, console checks, and MCP
  vs file-edit rules.
- `guides/input-system.md`: New Input System as the default input approach for
  new Unity projects.
- `guides/serialization-safety.md`: serialized field rename, prefab, asset, and
  ScriptableObject safety rules.

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
8. Create a phase/task plan in `.cursor/project/WORKFLOW.md`.
9. Ask Cursor to dry-run, validate, or execute the workflow.

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
| `.cursor/rules/*.mdc` | Cursor Project Rules. |
| `.cursor/core/agents/` | Reusable role templates for implementation, tests, review, and commits. |
| `.cursor/core/commands/` | Reusable workflow commands such as dry run, orchestrate, continue, status, stop, and validate. |
| `.cursor/core/protocols/` | Reusable runtime state formats. |
| `.cursor/project/PROJECT.md` | Project identity, goals, enabled packs. |
| `.cursor/project/STRUCTURE.md` | Folder layout, modules, ownership, generated files. |
| `.cursor/project/TOOLING.md` | Build, test, lint, format, and Unity commands. |
| `.cursor/project/RULES.md` | Repository-specific hard and soft rules. |
| `.cursor/project/CODING_CONVENTIONS.md` | Concrete coding style for the project. |
| `.cursor/project/WORKFLOW.md` | Phase/task execution plan. |
| `.cursor/project/PROGRESS.md` | Human-readable workflow status. |
| `.cursor/packs/unity-game/` | Unity-specific reusable guidance. |
| `.cursor/packs/unity-game/README.md` | Unity pack overview. |
| `.cursor/packs/unity-game/guides/unity-mcp.md` | Unity MCP workflow and verification loop. |
| `.cursor/packs/unity-game/guides/input-system.md` | New Input System default guidance. |
| `.cursor/packs/unity-game/guides/serialization-safety.md` | Unity serialized data safety rules. |

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
