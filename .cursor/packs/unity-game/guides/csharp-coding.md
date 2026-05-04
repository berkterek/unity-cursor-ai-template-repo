# C# Coding Guide — Unity Game

## Naming Summary

| Construct | Style | Example |
|-----------|-------|---------|
| Class, struct, enum | PascalCase | `AudioService`, `ProductType` |
| Interface | `I` + PascalCase | `IAudioService` |
| Method, property | PascalCase | `PlaySound()`, `IsPlaying` |
| Private / protected field | `_` + camelCase | `_audioService` |
| Public field (`[Serializable]` only) | PascalCase | `SfxVolume` |
| Local variable, parameter | camelCase | `currentLevel` |
| Constant | `SCREAMING_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| `static readonly` | PascalCase | `JumpHash`, `DefaultColor` |
| IEvent struct | PascalCase + past tense + `Event` | `LevelStartedEvent` |
| ScriptableObject | PascalCase + descriptive suffix | `AudioConfiguration` |
| Installer | PascalCase + `Installer` | `AudioInstaller` |
| Namespace | `<Layer>.<Module>` | `Framework.Events` |
| Test class | PascalCase + `Tests` | `EnemySpawnerTests` |
| Test method | `MethodName_WhenCondition_ExpectedBehavior` | `TakeDamage_WhenZeroHealth_RaisesEvent` |
| ECS data component | PascalCase, no suffix | `HealthData`, `MoveSpeed` |
| ECS tag component | PascalCase + `Tag` | `EnemyEntityTag` |
| ECS cleanup component | PascalCase + `CleanupData` | `EnemyCleanupData` |
| ECS Authoring | PascalCase + `Authoring` | `EnemyAuthoring` |

---

## Namespace Convention

Format: `<Layer>.<Module>` — underscore prefix on folder names is dropped.

| Folder | Namespace |
|--------|-----------|
| `_Framework/Events/` | `Framework.Events` |
| `_GameFolders/Scripts/Games/` | `Game` |
| `_GameFolders/Scripts/Games/Abstracts/` | `Game.Abstracts` |
| `_GameFolders/Scripts/Games/Concretes/` | `Game.Concretes` |
| `_GameFolders/Scripts/Games/Ecs/` | `Game.Ecs` |

---

## Field Declarations

```csharp
private IAudioService _audioService;           // private — _camelCase
private bool _isInitialized;
private readonly IEventBus _eventBus;

private static readonly int JumpHash = Animator.StringToHash("Jump");   // static readonly — PascalCase
private static readonly int ColorId  = Shader.PropertyToID("_Color");

private const int    MAX_RETRY_COUNT  = 3;     // constant — SCREAMING_SNAKE_CASE

[SerializeField] private float _moveSpeed;     // Inspector field — only when designer tweaks it
```

---

## Encapsulation (NON-NEGOTIABLE)

Everything is `private` unless a concrete caller requires otherwise.

```csharp
// BAD — speculative public API
public class EnemyService
{
    public EnemyModel Model;
    public void Initialize() { }
}

// GOOD — minimum visibility
public sealed class EnemyService
{
    private readonly EnemyModel _model;
    private void Initialize() { }
    public void TakeDamage(int amount) { }   // CombatService calls this
}
```

---

## #region Order (Required in `_GameFolders/Scripts/`)

```csharp
public class ExampleService : IExampleService, IDisposable
{
    #region Fields
    private readonly IEventBus _eventBus;
    #endregion

    #region Constructor
    public ExampleService(IEventBus eventBus) { _eventBus = eventBus; }
    #endregion

    #region Lifecycle
    public void Initialize() { }
    public void Dispose() { }
    #endregion

    #region Public Methods
    public void DoSomething() { }
    #endregion

    #region Private Methods
    private void InternalHelper() { }
    #endregion
}
```

**Exception:** Interface files, single-member structs/enums, and classes with fewer than 3 methods do not require `#region`.

---

## Null Check Rules

```csharp
// Plain C# objects — standard C# null operators are fine
_eventBus?.Publish(new LevelStartedEvent());
if (_provider == null) return;

// Unity objects (MonoBehaviour, ScriptableObject, etc.) — MUST use == null
// Unity overrides == to detect destroyed objects; ?. and is null bypass this
if (_target == null) return;       // CORRECT
if (_target is null) return;       // WRONG — misses destroyed objects
_target?.TakeDamage(10);           // WRONG — calls method on destroyed objects
```

---

## Async Rules — UniTask Only

```csharp
// GOOD
public async UniTask InitializeAsync(CancellationToken ct)
{
    await UniTask.Delay(1000, cancellationToken: ct);
}

// BAD — coroutine
IEnumerator Initialize() { yield return new WaitForSeconds(1f); }

// BAD — async void (swallows exceptions)
async void Initialize() { }
```

**Exception — Test Assemblies:** `[UnityTest]` requires `IEnumerator` — this is a Unity test runner constraint, not a violation.

### CancellationToken Lifecycle

Every async method takes a `CancellationToken`. Bind to lifecycle:

```csharp
public sealed class StoreService : IInitializable, IDisposable
{
    private CancellationTokenSource _cts;

    public void Initialize()
    {
        _cts = new CancellationTokenSource();
        SetupAsync(_cts.Token).Forget();
    }

    public void Dispose()
    {
        _cts?.Cancel();
        _cts?.Dispose();
    }
}
```

### Fire-and-Forget

```csharp
// GOOD — error handler prevents silent failures
InitializeAsync(ct).Forget(e => Debug.LogException(e));

// BAD — exception silently lost
InitializeAsync(ct).Forget();
```

---

## Control Flow

- Braces always, even for single-line `if`/`for`/`while`.
- Early return over deep nesting (guard clauses).
- `for` over `foreach` in hot paths (Update, FixedUpdate).
- No magic strings — use `nameof()`, `Animator.StringToHash()`, `Shader.PropertyToID()`.
- No LINQ in gameplay code.
- `CompareTag("tag")` not `tag == "tag"`.

---

## Types and File Rules

- `sealed` by default — only unseal when inheritance is explicitly designed.
- One type per file — file name MUST match the primary class/struct name.
- Explicit access modifiers everywhere.
- Use `var` when the type is obvious from the right-hand side.
