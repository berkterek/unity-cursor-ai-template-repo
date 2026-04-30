# Cursor AI Workflow Template

This directory is a reusable Cursor workflow template. It separates Cursor
project rules from repository-specific overlays and technology-specific packs.

## Layout

| Path | Purpose |
|------|---------|
| `rules/` | Cursor Project Rules in `.mdc` format. Cursor reads these. |
| `core/` | Reusable agent, command, and protocol templates. |
| `project/` | Per-repository overlay files. Fill these for each project. |
| `templates/` | Copyable templates for project overlays. |
| `packs/` | Optional technology/domain packs, such as Unity. |
| `manifests/` | Import notes and template decisions. |

## How Cursor Should Use This

Cursor Project Rules live in `rules/` and should stay short enough to be active
guidance. Detailed reusable workflow behavior lives in `core/`.

Use the layers in this order:

1. `rules/` for always-on routing and Cursor behavior.
2. `project/` for this repository's actual constraints and commands.
3. `core/agents/` for role-specific behavior.
4. `core/commands/` for workflow operations such as dry run, orchestrate,
   continue, status, stop, and validate.
5. `core/protocols/` for progress, event, mailbox, heartbeat, and checkpoint
   formats.
6. `packs/` for technology-specific reusable guidance.

Keep reusable guidance in `core/` or `packs/`. Keep repository facts in
`project/`.

## First-Time Setup In A Project

After copying `.cursor/` into a repository, fill the project overlay files in
this order:

1. `project/PROJECT.md`
   - Project name, type, language, framework, target platforms.
   - Enabled packs, such as `unity-game`.
   - High-level goals and constraints.

2. `project/STRUCTURE.md`
   - Source roots.
   - Folder layout.
   - Module map.
   - Dependency boundaries.
   - File ownership rules.
   - Generated files and project-specific patterns.

3. `project/TOOLING.md`
   - Install, build, test, lint, format, and dev-server commands.
   - Narrow verification command for small changes.
   - Full verification command for risky/shared changes.

4. `project/CODING_CONVENTIONS.md`
   - Copy from `templates/CODING_CONVENTIONS.md` if useful.
   - Fill naming, namespace, async, DI, test, and platform-specific style.

5. `project/RULES.md`
   - Hard rules that block completion.
   - Soft rules that guide implementation.
   - Security, performance, documentation, and review rules.

6. `project/WORKFLOW.md`
   - Phase/task plan.
   - Inputs, outputs, acceptance criteria, dependencies, and parallel groups.
   - This is what the Cursor Agent executes when asked.

7. `project/PROGRESS.md`
   - Starts as `not-started`.
   - Update during execution.
   - Use `project/EVENTS.jsonl` as append-only recovery history once created.

## Typical Cursor Prompts

```text
Read .cursor/README.md and .cursor/project/*.md, then tell me what is missing
before this project is ready for workflow execution.
```

```text
Preview .cursor/project/WORKFLOW.md as a dry run. Do not modify files.
```

```text
Execute Phase 1 from .cursor/project/WORKFLOW.md. Follow Cursor rules, project
overlays, and enabled packs.
```

```text
Report the current workflow status from .cursor/project/PROGRESS.md and recent
events.
```

```text
Resume the workflow from .cursor/project/EVENTS.jsonl and .cursor/runtime
checkpoints.
```

## What Goes Where

| Need | Put It Here |
|------|-------------|
| Cursor behavior and reusable instructions | `rules/` |
| Detailed reusable agent roles | `core/agents/` |
| Detailed reusable workflow commands | `core/commands/` |
| Reusable runtime state protocols | `core/protocols/` |
| Project identity and goals | `project/PROJECT.md` |
| Folder/module/ownership map | `project/STRUCTURE.md` |
| Build/test/lint commands | `project/TOOLING.md` |
| Coding style | `project/CODING_CONVENTIONS.md` |
| Repo-specific hard rules | `project/RULES.md` |
| Phase/task execution plan | `project/WORKFLOW.md` |
| Runtime status | `project/PROGRESS.md` and `project/EVENTS.jsonl` |
| Technology-specific guidance | `packs/<pack-name>/` |
| Reusable fill-in templates | `templates/` |

## Separation Rule

- Rules answer how Cursor should behave.
- Project overlay answers how this repository is organized and verified.
- Packs answer how a specific technology stack should be handled.

When unsure where something belongs, prefer `project/` first. Promote it to a
pack only after it proves reusable across multiple repositories.

## Practical Rule

Do not start by editing reusable rules. Start by filling `project/`.

If an instruction is true only for one repository, put it in `project/`.
If it is true for one technology stack, put it in `packs/`.
If it is true for every repository using this workflow, then it can belong in
`rules/`.
