# Explosion Damage

**Type:** Exec (In → Out)  
**Category:** PLAYER

Applies radial damage with falloff to all objects within a radius at the moment of detonation. Optionally applies a physical impulse pushing objects away from the explosion center.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Radius | Float | `5.0` | Explosion radius (Range units) |
| Max Damage | Float | `100.0` | Maximum damage at the center (distance 0) |
| Min Damage | Float | `0.0` | Minimum damage at the edge of the radius |
| Property Filter | String | `""` | Only damages objects with this BGE property |
| Trigger | Enum | `On Spawn` | When the explosion fires |
| Allow Retrigger | Bool | `False` | Allows the explosion to fire more than once |
| Apply Impulse | Bool | `False` | Physically pushes objects away |
| Impulse Force | Float | `500.0` | Maximum impulse force at center |
| Line of Sight | Bool | `False` | Skips objects blocked by geometry |

### Trigger modes

| Mode | Description |
|------|-------------|
| `Projectile Hit` | Fires when `projectile_hit = True` |
| `Manual` | Fires when `explosion_requested = True` |
| `On Spawn` | Fires once on the first execution frame |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Damage falloff

Damage is linearly interpolated between `Max Damage` (distance 0) and `Min Damage` (distance = Radius):

```
damage = Max Damage - (Max Damage - Min Damage) * (distance / Radius)
```

Damage is applied via `globalDict['_dmg_<object_name>']` for each object in the radius.

## Typical usage

Place on the explosive projectile object, after Projectile System:

```
[OnUpdate] → [Projectile System]
                 └── Out → [Explosion Damage: Trigger=Projectile Hit]
```

Or on a grenade object that explodes on spawn:

```
[OnUpdate] → [Explosion Damage: Trigger=On Spawn]
```
