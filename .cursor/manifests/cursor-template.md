# Cursor Template Manifest

This `.cursor` template adapts the repository workflow to Cursor Project Rules.

## Main Decisions

- Cursor-readable instructions live in `.cursor/rules/*.mdc`.
- Detailed reusable roles, commands, and protocols live in `.cursor/core/`.
- Repository-specific overlays live in `.cursor/project/`.
- Technology-specific reusable guidance lives in `.cursor/packs/`.
- Fill-in templates live in `.cursor/templates/`.
- Runtime state should live in `.cursor/runtime/` when needed.

## Core Files

- `core/agents/`: reusable role definitions for implementation, testing,
  review, and commits.
- `core/commands/`: reusable workflow operations for dry run, orchestration,
  continue, status, stop, and validation.
- `core/protocols/`: reusable state formats for progress, events, mailbox,
  heartbeat, and checkpoints.

## Rule Files

- `000-template-entry.mdc`: always applied entry rule.
- `010-project-overlay.mdc`: project overlay separation rule.
- `020-workflow-execution.mdc`: workflow execution and state protocol.
- `030-review-quality.mdc`: review and validation rule.
- `040-unity-pack.mdc`: Unity-specific pack routing rule.
- `050-implementation.mdc`: implementation routing rule.
- `060-testing.mdc`: testing routing rule.
- `070-commit.mdc`: commit routing rule.

## Separation Contract

- `rules/` should stay Cursor-readable and concise.
- `core/` should hold reusable detailed procedure.
- `project/` should hold facts and decisions for one repository.
- `packs/` should hold reusable technology-specific procedure.
