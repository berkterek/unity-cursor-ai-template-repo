# Coding Conventions Template

This file is a fill-in template for repository-specific coding conventions.
Copy relevant sections into `.cursor/project/CODING_CONVENTIONS.md` and edit
them for the current project.

## Project Decisions

| Decision | Choice |
|----------|--------|
| Root namespace | `[ROOT_NAMESPACE]` |
| Runtime source root | `[RUNTIME_SOURCE_ROOT]` |
| Editor/tools source root | `[EDITOR_OR_TOOLS_SOURCE_ROOT]` |
| Test source root | `[TEST_SOURCE_ROOT]` |
| Main language/runtime | `[language/runtime]` |
| DI strategy | `[strategy]` |
| Async strategy | `[strategy]` |
| Event strategy | `[strategy]` |
| Test framework | `[framework]` |
| Mocking policy | `[policy]` |
| Formatting tool | `[tool]` |

## Naming

### Types

```csharp
public class AudioService { }
public interface IAudioService { }
public struct PurchaseResult { }
public enum ProductType { Consumable }
```

Rules:

- Classes, structs, records, enums: `PascalCase`.
- Interfaces: `I` + `PascalCase`.
- Enum members: `PascalCase`.
- Avoid vague names unless they are established by the project.

### Methods And Properties

```csharp
public void PlaySound(string id) { }
public bool IsPlaying { get; private set; }
public int Order => 100;
```

Rules:

- Methods: `PascalCase`.
- Properties: `PascalCase`.
- Public APIs should communicate intent through names.

### Fields

```csharp
private IAudioService _audioService;
private bool _isInitialized;
private readonly IEventBus _eventBus;
private const int MAX_RETRY_COUNT = 3;
```

Rules:

- Private/protected fields: `_camelCase`.
- Private readonly fields: `_camelCase`.
- Constants: `SCREAMING_SNAKE_CASE`.
- Public fields are avoided by default unless the project explicitly permits
  them for serializable data/config.

### Locals And Parameters

```csharp
var audioService = new AudioService();
public void Initialize(IAudioService audioService) { }
```

Rules:

- Locals: `camelCase`.
- Parameters: `camelCase`.
- Prefer names that describe role, not type repetition.

### Events And Messages

Choose one event policy and keep it consistent.

```csharp
public event Action OnLevelCompleted;
public readonly struct LevelStartedEvent { }
public readonly struct CoinsChangedMessage { }
```

Rules:

- C# events: `PascalCase`.
- Event/message structs: past-tense domain name + `Event` or `Message` suffix.
- Avoid command-style event names such as `StartLevelEvent`.

## Namespace Policy

Choose one mapping rule and apply it consistently.

### Folder-Based Namespace

```text
src/Framework/Events -> Framework.Events
src/Game/Systems     -> Game.Systems
```

### Root Namespace + Feature

```text
src/Audio -> [ROOT_NAMESPACE].Audio
src/Store -> [ROOT_NAMESPACE].Store
```

Rules:

- Namespace follows the chosen project folder policy.
- Third-party libraries keep their own namespaces.
- Do not mix multiple namespace policies in one project.

## File Structure

Rules:

- One primary type per file.
- File name matches primary type.
- Exceptions must be documented here.

Recommended member order:

```csharp
public sealed class ExampleService
{
    // Constants
    // Static readonly fields
    // Serialized/config fields
    // Private fields
    // Properties
    // Constructor
    // Lifecycle methods
    // Public methods
    // Private methods
}
```

## Region Policy

Pick one:

- `none`: no regions, prefer short files.
- `large-files-only`: regions allowed for large UI/tooling files.
- `required`: specific folders require standard regions.

If required, define the exact region order here.

## Null Checks

Rules:

- Plain objects may use standard language null features.
- Platform objects with custom destroyed/null semantics must use the platform's
  safe null check pattern.
- Document exceptions here.

## Async Policy

Choose one:

- `Task`
- `UniTask`
- coroutine/lifecycle based
- no async

Rules:

- Do not block async calls with `.Result` or `.Wait()` unless explicitly
  approved.
- Long-running async operations need cancellation or ownership rules.
- Fire-and-forget calls need an error-handling policy.

## Dependency Policy

Rules:

- Composition root owns object graph construction.
- Runtime services use constructor injection when possible.
- Consumers depend on interfaces when crossing module boundaries.
- Avoid service locator and global mutable state unless explicitly approved.
- Do not inject a large context object that exposes unrelated dependencies.

## Event Subscription Lifecycle

Document the lifecycle for each class type.

| Class type | Subscribe | Unsubscribe |
|------------|-----------|-------------|
| Plain service | `[initialize/constructor]` | `[dispose]` |
| UI/view/component | `[enable/init]` | `[disable/dispose]` |
| Manually owned object | owner-controlled | owner-controlled |

Rules:

- Every subscription must have a matching cleanup path.
- Event handlers should be private unless external API requires otherwise.

## Layer And Module Dependencies

Define allowed dependencies:

```text
[App layer] -> [Domain layer] -> [Core layer]
```

Rules:

- Core/framework code must not reference project-specific concepts.
- Modules communicate through approved boundaries.
- Cross-module concrete dependencies are not allowed unless documented.

## Portable Module Template

Use when the project wants copy-paste portable modules:

```text
FeatureName/
├── IFeatureNameService.cs
├── FeatureNameService.cs
├── FeatureNameConfiguration.cs
├── FeatureNameInstaller.cs
└── FeatureNameEvents.cs
```

Rules:

- Interface is the public API.
- Service implementation is sealed unless inheritance is explicitly designed.
- Configuration belongs to the selected project config system.
- Installer/registration is the only setup point.
- Platform-specific adapters live at the boundary.

## Defensive Programming

### Fail Fast For Required Configuration

```csharp
if (_config == null)
{
    throw new InvalidOperationException("Required config is missing.");
}
```

### Prefer Guard Clauses

```csharp
public void OnPointerDown(Vector2 position)
{
    if (!_isEnabled) return;
    if (_isBlocked) return;

    ProcessInput(position);
}
```

### Use Try/Finally For Guaranteed Cleanup

```csharp
ShowLoading();
try
{
    await PurchaseAsync(productId, cancellationToken);
}
finally
{
    HideLoading();
}
```

## Testing Conventions

| Decision | Choice |
|----------|--------|
| Unit test framework | `[framework]` |
| Integration test framework | `[framework]` |
| Mocking | `[hand-written fakes / NSubstitute / Moq / none]` |
| Test location | `[path]` |

Rules:

- Test file name: `[TestedClass]Tests.cs`.
- Test method name: `MethodName_WhenCondition_ExpectedBehavior`.
- Tests use Arrange / Act / Assert when the project requires it.
- Mock interfaces or explicit seams, not concrete internals.
- Each test owns its setup and cleanup.

## Unity Overlay

Use only for Unity projects.

### Serialized Fields

Rules:

- Inspector references follow the selected serialization policy.
- Runtime state and cached references should not be serialized unless required.
- Renamed serialized data needs migration handling.

### Object Creation

Pick one policy:

- `pool-only`
- `prefab-instantiate-allowed`
- `standard-unity`

Selected:

```text
Runtime object policy: [policy]
```

### Editor Code

Rules:

- Editor-only scripts live in editor-only folders or assemblies.
- Runtime code cannot reference editor-only APIs.
- If editor API is unavoidable in runtime folders, guard it explicitly.

## ECS/DOTS Overlay

Use only when Unity ECS/DOTS is enabled.

Rules:

- Entities are created through the approved project pattern.
- Authoring and baker patterns are documented.
- Structural changes use the approved command buffer pattern.
- System update order is explicit when ordering matters.
- Burst/hot path restrictions are documented per Entities version.

## Summary Table

| Item | Style |
|------|-------|
| Class/struct/enum | `PascalCase` |
| Interface | `I` + `PascalCase` |
| Method/property | `PascalCase` |
| Private field | `_camelCase` |
| Local/parameter | `camelCase` |
| Constant | `SCREAMING_SNAKE_CASE` |
| Test class | `[ClassName]Tests` |
| Test method | `MethodName_WhenCondition_ExpectedBehavior` |
| Event/message | past-tense + `Event` or `Message` suffix |
| File name | primary type name |

