# ECS DOTS Guide — Unity Game

## 1. Entity Creation Rule

- Every entity is a **prefab**: defined in SubScene as Authoring GO + Baker, or instantiated from a prefab at runtime.
- `EntityManager.CreateEntity()` with no source prefab is **forbidden**.
- Entity prefabs live in SubScene as Authoring GameObjects.

---

## 2. Authoring & Baker Rule

Every entity has an **Authoring** MonoBehaviour and a matching **Baker** class.

- All inspector-configurable components are added through the Baker.
- Runtime-dependent data → added by the relevant **System**, not the Baker.

```csharp
public class EnemyAuthoring : MonoBehaviour
{
    public EnemyConfigSO Config;

    public class Baker : Baker<EnemyAuthoring>
    {
        public override void Bake(EnemyAuthoring authoring)
        {
            var entity = GetEntity(TransformUsageFlags.Dynamic);
            AddComponent(entity, new EnemyEntityTag());
            // Config data added by EnemyInitSystem, not here
        }
    }
}
```

---

## 3. Component Naming

| Type | Rule | Example |
|------|------|---------|
| `IComponentData` (data) | PascalCase, no suffix | `HealthData`, `MoveSpeed` |
| `IComponentData` (tag) | PascalCase + `Tag` | `EnemyEntityTag`, `DestroyEntityTag` |
| `IEnableableComponent` | Same: `Tag` suffix | `PauseStateTag` |
| `ICleanupComponentData` (value) | PascalCase + `CleanupData` | `EnemyCleanupData` |
| Managed `ICleanupComponentData` | PascalCase + `Reference` (class) | `EnemyVisualReference` |
| Authoring | PascalCase + `Authoring` | `EnemyAuthoring` |
| Baker | Inner class `Baker<T>` | `EnemyAuthoring.Baker` |
| `ISystem` (Burst) | PascalCase + `System` | `EnemyMoveSystem` |
| `SystemBase` (bridge) | PascalCase + `BridgeSystem` | `InputBridgeSystem` |

---

## 4. Hybrid ECS ↔ OOP Linking

Use a **managed `ICleanupComponentData` class** to link an entity to its MonoBehaviour.

```csharp
// 1 — Define managed reference component
public class TowerBaseVisualReference : ICleanupComponentData
{
    public TowerBaseProvider Value;
}

// 2 — Attach in Baker or init system
AddComponentObject(entity, new TowerBaseVisualReference { Value = provider });

// 3 — System reads it and calls the MonoBehaviour
protected override void OnUpdate()
{
    Entities.ForEach((TowerBaseVisualReference reference, in HealthData health) =>
    {
        reference.Value.UpdateHealthBar(health.Current, health.Max);
    }).WithoutBurst().Run();
}
```

---

## 5. ScriptableObject → Component Transfer

Enemy stats and config data live in ScriptableObjects. At runtime, a **System** reads the SO and copies values into entity components. The entity never holds a SO reference after init.

---

## 6. Mono ↔ ECS Communication Rule

No class talks directly to `ISystem` (preserves Burst compatibility).

| Direction | Chain |
|-----------|-------|
| Mono → ECS | `Mono class` → `SystemBase` → `ISystem` |
| ECS → Mono | `ISystem` → `SystemBase` → `Mono class` |

---

## 7. System Update Order

Every system declares its group explicitly with `[UpdateInGroup]`, `[UpdateBefore]`, `[UpdateAfter]`.

| Order | Group | Use |
|-------|-------|-----|
| 1 | `InitializationSystemGroup` | First-time config write to entity |
| 2 | `SimulationSystemGroup` — before `TransformSystemGroup` | Movement, velocity, input |
| 3 | `SimulationSystemGroup` — after `TransformSystemGroup` | Position query, range check, attack |
| 4 | `SimulationSystemGroup` — after attack | Damage accept, enable `DestroyEntityTag` |
| 5 | `LateSimulationSystemGroup` — before `DestroySystem` | Pre-destroy: add `ICleanupComponentData` |
| 6 | `LateSimulationSystemGroup` | Destroy entity |
| 7 | `LateSimulationSystemGroup` — after `DestroySystem` | Cleanup: OOP bridge |

---

## 8. ISystem + IJobEntity Rule

`ISystem` (Burst-compiled) cannot use `foreach` directly. Use `IJobEntity` + `ScheduleParallel`.

```csharp
[BurstCompile]
[UpdateInGroup(typeof(SimulationSystemGroup))]
[UpdateBefore(typeof(TransformSystemGroup))]
public partial struct EnemyMoveSystem : ISystem
{
    [BurstCompile]
    public void OnUpdate(ref SystemState state)
    {
        var job = new MoveJob { DeltaTime = SystemAPI.Time.DeltaTime };
        var handle = job.ScheduleParallel(state.Dependency);
        state.Dependency = handle;
        handle.Complete();
    }

    [BurstCompile]
    [WithDisabled(typeof(PauseStateTag))]
    partial struct MoveJob : IJobEntity
    {
        public float DeltaTime;

        void Execute(
            ref LocalTransform localTransform,
            in  MoveSpeedData  moveSpeed,
            in  TargetData     target,
            in  MoveStateTag   moveStateTag)
        {
            var direction = math.normalize(target.Position - localTransform.Position);
            localTransform.Position += DeltaTime * moveSpeed.Value * direction;
        }
    }
}
```

### IEnableableComponent Filtering

| Condition | Method |
|-----------|--------|
| Component must be **enabled** | Add as `Execute` parameter: `in MoveStateTag moveStateTag` |
| Component must be **disabled** | Add attribute: `[WithDisabled(typeof(PauseStateTag))]` |

---

## 9. Structural Change Rule

Entity creation, destruction, adding/removing components must use `EntityCommandBuffer`.

| Operation | Required method |
|-----------|----------------|
| `AddComponent`, `RemoveComponent`, `Instantiate`, `DestroyEntity` | `EntityCommandBuffer` |
| Enable / disable `IEnableableComponent` | `SystemAPI.SetComponentEnabled` |
| Data update only (`SetComponentData`, buffer content) | Direct write is fine |

```csharp
var ecb = new EntityCommandBuffer(Allocator.Temp);
ecb.AddComponent(entity, new TowerBaseCleanupData { Position = transform.ValueRO.Position });
ecb.Playback(EntityManager);
ecb.Dispose();
```

---

## 10. Folder Structure

```
_GameFolders/Scripts/Games/Ecs/
├── Authorings/    ← Authoring MonoBehaviours + Baker inner classes
├── Components/    ← IComponentData structs, tag components
└── Systems/       ← ISystem, SystemBase, bridge systems
```

ECS components and systems never go into `Abstracts/` or `Concretes/`.
