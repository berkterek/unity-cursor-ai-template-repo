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
- `080-architecture.mdc`: VContainer DI, module structure, IEventBus routing rule.
- `090-performance.mdc`: zero-alloc hot paths, caching, pooling, rendering routing rule.
- `100-ecs-dots.mdc`: ECS/DOTS authoring, systems, ECB, hybrid OOP routing rule.
- `110-addressables.mdc`: Addressables loading, handle lifecycle routing rule.

## Unity Pack Files

- `packs/unity-game/agents/unity-setup.md`: Unity Editor, scene, prefab,
  asset, package, and runtime setup guidance.
- `packs/unity-game/guides/guardrails.md`: high-risk Unity safety checklist.
- `packs/unity-game/guides/unity-mcp.md`: Unity MCP workflow, batching,
  console checks, and MCP vs file-edit rules.
- `packs/unity-game/guides/input-system.md`: New Input System as the default
  input approach for new Unity projects.
- `packs/unity-game/guides/serialization-safety.md`: serialized field rename,
  prefab, asset, and ScriptableObject safety rules.
- `packs/unity-game/guides/architecture.md`: VContainer DI, module structure,
  IEventBus pattern, MonoBehaviour adapter pattern.
- `packs/unity-game/guides/csharp-coding.md`: naming conventions, namespace
  policy, async/UniTask rules, null check rules, #region order.
- `packs/unity-game/guides/performance.md`: zero-alloc hot paths, caching,
  MaterialPropertyBlock, UI Canvas splitting, object pooling.
- `packs/unity-game/guides/ecs-dots.md`: Authoring/Baker, component naming,
  ISystem+IJobEntity, ECB structural changes, hybrid OOP-ECS linking.
- `packs/unity-game/guides/addressables.md`: no Resources.Load, async loading,
  handle lifecycle, address constants class.
- `manifests/skills-review.md`: review of which source skills belong in the
  base template and which should stay optional.

## Separation Contract

- `rules/` should stay Cursor-readable and concise.
- `core/` should hold reusable detailed procedure.
- `project/` should hold facts and decisions for one repository.
- `packs/` should hold reusable technology-specific procedure.
