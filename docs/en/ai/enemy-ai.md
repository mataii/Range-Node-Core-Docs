# Enemy AI

**Type:** Exec (In → Out)  
**Category:** AI

All-in-one prefab node for enemies. Encapsulates detection, patrol, chase, cover, attack, flee, and death in a single node. Ideal for prototyping or mid-complexity enemies without configuring the modular system.

## Property sections

### Basic

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Player | Enum | *(scene objects)* | Player object |
| Physics | Enum | `Character` | Enemy physics mode |
| Faction | String | `"enemy"` | Group faction for shared alerts |

### Stats / Difficulty

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Max HP | Float (≥1) | `100.0` | Base HP |
| HP × | Float (≥0.01) | `1.0` | HP multiplier |
| Damage × | Float (≥0.01) | `1.0` | Damage multiplier |
| Speed × | Float (≥0.01) | `1.0` | Speed multiplier |

Final HP = `Max HP × HP ×`. Damage and speeds are also scaled at compile time.

### Detection

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Vision Radius | Float (≥0.1) | `12.0` | Maximum vision range |
| Vision Angle | Float (1–360) | `90.0` | Total vision cone width |
| Aggro Radius | Float (≥0.0) | `2.5` | Auto-detect radius without LOS check |
| Lose Target (s) | Float (≥0.1) | `3.0` | Seconds until target is lost |

### Movement

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Patrol Speed | Float (≥0.0) | `2.0` | Patrol speed |
| Chase Speed | Float (≥0.0) | `4.0` | Chase speed |
| Flee Speed | Float (≥0.0) | `5.0` | Flee speed |
| Rotation Lerp | Float (≥0.001) | `0.1` | Rotation smoothing |
| Move Smoothing | Float (0.01–1.0) | `0.12` | Movement smoothing |
| Obstacle Avoidance | Bool | `True` | Forward raycast to steer around obstacles |

### Patrol Waypoints

| Property | Description |
|----------|-------------|
| WP 1 … WP 6 | Waypoint objects in visit order |
| Mode | `Loop` (cyclic) or `Ping Pong` (back and forth) |

### Combat

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Attack Type | Enum | `Melee` | Melee or Ranged |
| Attack Range | Float (≥0.1) | `2.0` | Attack distance |
| Cooldown (s) | Float (≥0.05) | `1.5` | Seconds between attacks |
| Damage | Float (≥0.0) | `10.0` | Damage per attack |
| Projectile | Enum | *(scene objects)* | Object to addObject() for Ranged attacks |
| Strafe | Bool | `True` | Move sideways while chasing |

### Cover

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Cover 1 … 4 | Enum | *(scene objects)* | Cover objects |
| Take Cover at HP % | Float (0–1) | `0.5` | HP fraction to enter cover |
| Select By | Enum | `Nearest` | Selection criterion (Nearest / Farthest / Best Blocking) |
| Peek Duration (s) | Float (≥0.1) | `2.0` | Seconds in cover before peeking to attack |

### Thresholds

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Flee at HP % | Float (0–1) | `0.2` | HP fraction to flee |
| Stagger (s) | Float (≥0.0) | `0.3` | Stagger duration when hit |
| Knockback Force | Float (≥0.0) | `5.0` | Knockback force when hit |

### Group Behavior

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Alert Others | Bool | `True` | Broadcast player position to same-faction enemies |

### Audio

| Property | Description |
|----------|-------------|
| Alert Sound | Sound on first sighting the player |
| Attack Sound | Sound on attack |
| Death Sound | Sound on death |

### Animations

| Property | Description |
|----------|-------------|
| Idle | Range action name for idle |
| Walk | Range action name for movement |
| Attack | Range action name for attack |
| Die | Range action name for death |
| Layer | Range animation layer |

### Death / Cleanup

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Death Type | Enum | `Disappear` | `Disappear` / `Stay` / `Sink` |
| Sink Speed | Float (≥0.01) | `0.5` | Sinking speed if Sink |
| Drop Object | Enum | *(scene objects)* | Object to spawn on death |
| Cleanup Delay (s) | Float (≥0.0) | `3.0` | Seconds before cleanup executes |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## States

```
idle → patrol → alert → chase → cover ↔ attack → flee → dead
```

| State | Entry condition |
|-------|----------------|
| idle | No waypoints or lost the player |
| patrol | Has waypoints, no player visible |
| alert | Received faction alert (no LOS) |
| chase | Player visible, outside attack range |
| cover | HP < cover_threshold and has cover objects |
| attack | Within attack range; after peek from cover |
| flee | HP < flee_threshold |
| dead | HP ≤ 0 |

## Damage channel

**Enemy AI uses its own channel**, separate from the modular system:

```python
# To deal damage to this enemy:
Range.logic.globalDict['_ai_dmg_' + enemy.name] = amount

# With knockback:
Range.logic.globalDict['_ai_dmg_' + enemy.name] = amount
Range.logic.globalDict['_ai_dmg_src_' + enemy.name] = list(source.worldPosition)
```

> Do not use `_dmg_<name>` — that channel belongs to the modular system's Damage Receiver.

## Scene properties published (BGE properties)

| Property | Type | Description |
|----------|------|-------------|
| `ai_state` | String | Current state (for native animations) |
| `ai_hp` | Float | Current HP |
| `ai_attacking` | Bool | True the frame an attack fires |

## Group behavior (Alert Others)

When the enemy spots the player and `Alert Others = True`, it writes:
```python
globalDict['_ai_alert_<faction>'] = [x, y, z]
```

Other Enemy AI nodes with the same `Faction` that are in `idle` state will read this position and switch to `alert`.

## Typical usage

### Basic enemy without waypoints

```
Player: Player
Physics: Character
Max HP: 80.0
Vision Radius: 12.0
Attack Type: Melee
Damage: 15.0
Cooldown: 1.5
Flee at HP %: 0.2
Death Type: Disappear
Idle: "Idle"
Walk: "Walk"
Die: "Die"
```

### Soldier with cover and patrol waypoints

```
Player: Player
WP 1: Patrol_A
WP 2: Patrol_B
Mode: Ping Pong
Cover 1: Cover_Box
Take Cover at HP %: 0.5
Peek Duration: 2.5
Attack Type: Ranged
Projectile: Bullet_Prefab
```

## Comparison with the modular system

| Enemy AI (prefab) | Modular system |
|------------------|----------------|
| Single node | 10–11 nodes |
| Direct configuration | Requires connecting nodes |
| Channel `_ai_dmg_*` | Channel `_dmg_*` |
| Hardcoded states | Extensible with BTCondition/Custom |
| Best for prototyping | Best for production |

## Notes

- All multipliers (HP ×, Damage ×, Speed ×) are applied **at compile time**, not at runtime. Changing them requires recompiling the component.
- Obstacle Avoidance casts a 1.5-unit forward raycast every frame — disable on simple enemies to save CPU.
- If `Faction` is empty, the node defaults to `"enemy"`.
- Death Type `Stay` leaves the body in the scene and stops it (no endObject). `Disappear` calls `endObject()` after the delay. `Sink` moves it down the Z axis before disappearing.
