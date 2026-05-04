# Architecture Guide — Unity Game

## Core Principle: Dependency Direction

```
Views/Providers → Services → Models/Interfaces
       ↓               ↓
   IEventBus  (decoupled cross-system communication)
```

- Services depend on interfaces, never concrete types.
- MonoBehaviours (Views/Providers) depend on services via VContainer injection.
- Models/data classes depend on nothing.
- Cross-service communication goes through IEventBus, never direct references.
- Assembly definitions enforce direction at compile time.

---

## Layer Structure

```
_Framework/          ← No Unity dependency. Pure C# infrastructure.
  Events/            ← IEventBus, IEvent
  Logging/
  SaveLoadSystems/

_GameFolders/        ← Depends on _Framework. All game-specific code.
  Scripts/
    Games/
      Abstracts/     ← abstract classes, interfaces
      Concretes/     ← concrete implementations
      Ecs/           ← ECS DOTS systems, components, authorings
    Tests/
```

**Rule:** `_Framework` never references `_GameFolders`. `_GameFolders` may reference `_Framework`.

---

## Module Structure (NON-NEGOTIABLE)

Every service/system lives in its own folder with exactly these 5 files:

```
Audio/
├── IAudioService.cs       ← The only public API contract
├── AudioService.cs        ← sealed implementation
├── AudioConfiguration.cs  ← ScriptableObject config
├── AudioInstaller.cs      ← VContainer registration
└── AudioEvents.cs         ← IEvent structs for this module (if any)
```

Provider implementations live **outside** the module folder:

```
_GameFolders/Scripts/Games/Concretes/Audio/
├── BasicAudioProvider.cs  ← IAudioProvider impl (Unity API here)
└── AudioRoot.cs           ← MonoBehaviour, scene object
```

**Why:** The module folder is portable — copy-paste to another project. Concrete Unity providers are project-specific.

### Module Portability Checklist

Before exporting a module:

| Check | Description |
|-------|-------------|
| No `using UnityEngine` in service | Service file is pure C# |
| No concrete cross-module deps | Only interfaces in constructor |
| Config null guard in installer | `if (_config == null) throw` |
| Events in own file | Not embedded in service |
| Provider separated | In `Concretes/[Module]/` folder |

---

## VContainer Dependency Injection

### NO Singletons

```csharp
// BAD — singleton pattern
public class AudioManager : MonoBehaviour
{
    public static AudioManager Instance { get; private set; }
    private void Awake() { Instance = this; }
}

// GOOD — VContainer registration
public sealed class AudioService : IAudioService
{
    public AudioService(IAudioProvider provider) { }
}
```

### Scopes

| Scope | Lifetime | Use Case |
|-------|----------|----------|
| `AppScope` | App lifetime | Services shared across all scenes |
| Scene `LifetimeScope` | Scene lifetime | Services tied to one scene |
| Prefab `LifetimeScope` | Instance lifetime | Per-entity or per-UI panel |

### Registration Pattern

```csharp
public sealed class AudioInstaller : MonoBehaviour
{
    [SerializeField] private AudioConfiguration _config;

    public void Install(IContainerBuilder builder)
    {
        if (_config == null)
            throw new InvalidOperationException("AudioConfiguration is required.");

        builder.RegisterInstance(_config);
        builder.Register<IAudioService, AudioService>(Lifetime.Singleton);
    }
}
```

---

## IEventBus Pattern

Use IEventBus for all cross-module communication. Never inject a module's concrete service into another module — use events.

```csharp
// Define event in the publishing module's Events file
public readonly struct LevelStartedEvent : IEvent
{
    public int LevelIndex { get; }
    public LevelStartedEvent(int levelIndex) => LevelIndex = levelIndex;
}

// Publish
_eventBus.Publish(new LevelStartedEvent(currentLevel));

// Subscribe (in Initialize, unsubscribe in Dispose)
_eventBus.Subscribe<LevelStartedEvent>(OnLevelStarted);
_eventBus.Unsubscribe<LevelStartedEvent>(OnLevelStarted);
```

### Event Naming Rules

- Past-tense domain name + `Event` suffix: `LevelStartedEvent`, `EnemyDiedEvent`.
- Never command-style: ~~`StartLevelEvent`~~, ~~`KillEnemyEvent`~~.
- Defined as `readonly struct` implementing `IEvent`.

---

## MonoBehaviour Adapter Pattern

MonoBehaviours are THIN adapters. They hold serialized references and delegate to pure C# systems.

```csharp
public sealed class AudioRoot : MonoBehaviour
{
    [SerializeField] private AudioConfiguration _config;

    private IAudioService _audioService;

    [Inject]
    public void Construct(IAudioService audioService)
    {
        _audioService = audioService;
    }

    private void OnEnable()
    {
        _audioService.OnEnable();
    }

    private void OnDisable()
    {
        _audioService.OnDisable();
    }
}
```

**Rule:** Zero business logic in MonoBehaviours. They read input, render output, and bridge Unity lifecycle.

---

## EventBusAccessor (Exception to No-Static Rule)

The only allowed static pattern. Use for cross-scope event publishing when VContainer cannot bridge the scope.

```csharp
public static class EventBusAccessor
{
    public static IEventBus EventBus { get; private set; }

    public static void Initialize(IEventBus eventBus)
    {
        EventBus = eventBus;
    }
}
```

Initialized in `AppScope`. Never use for anything other than IEventBus access.

---

## No GameContext / Service Locator

Each class declares only its own dependencies in its constructor. No god object that exposes unrelated services.

```csharp
// BAD — service locator
public class EnemyService
{
    public EnemyService(IGameContext context)
    {
        _audioService = context.AudioService; // hidden dependency
    }
}

// GOOD — explicit dependency
public class EnemyService
{
    public EnemyService(IAudioService audioService)
    {
        _audioService = audioService;
    }
}
```
