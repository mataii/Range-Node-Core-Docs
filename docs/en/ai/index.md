# AI Category

The **AI** category contains the artificial intelligence nodes in Range Node Core. They cover everything from primitive movement behaviors to a complete modular system with perception, state management, navigation, and combat.

## Two Approaches

### Prefab approach — Enemy AI

A single **Enemy AI** node that encapsulates all logic: detection, patrol, chase, cover, attack, flee, and death. Best for quick prototyping or simple enemies.

### Modular approach — Enemy System

Eleven specialized nodes that can be combined and replaced independently. Each node publishes `ai_*` variables that others read via `getattr`. The execution order is predefined by `execution_order`.

```
[OnUpdate]
    │
    ├── [Enemy Perception]       execution_order=40
    ├── [Damage Receiver]        execution_order=42
    ├── [AI State Machine]       execution_order=50
    ├── [Enemy Decision]         execution_order=51  (branch)
    ├── [Navigation Pathfinder]  execution_order=52
    ├── [Navigation Follow Path] execution_order=54
    ├── [Enemy Movement]         execution_order=55
    ├── [Enemy Rotation]         execution_order=56
    ├── [Enemy Combat]           execution_order=58
    ├── [Enemy Animation]        execution_order=60
    └── [Enemy Hit Flash]        execution_order=62
```

### Primitive movement nodes

Seek, Flee, Patrol, and Wander are standalone nodes that don't depend on the modular system. They can be used alone or combined with BTCondition/BT nodes.

---

## `ai_*` variable contract

The `ai_*` variables are attributes on `self` (the Python component). They are read with `getattr(self, 'ai_*', default)` to avoid errors if the source node isn't in the scene.

| Variable | Owner (WRITES) | Type | Description |
|----------|----------------|------|-------------|
| `ai_can_see` | Enemy Perception | bool | Target visible this frame |
| `ai_has_line_of_sight` | Enemy Perception | bool | Unobstructed raycast to target |
| `ai_dist` | Enemy Perception | float | XY distance to target |
| `ai_target` | Enemy Perception | KX_GameObject\|None | Target object reference |
| `ai_target_pos` | Enemy Perception | Vector\|None | Target position if visible |
| `ai_last_seen_pos` | Enemy Perception | Vector\|None | Last known position (persists) |
| `ai_hp` | Damage Receiver | float | Current HP |
| `ai_hp_max` | Damage Receiver | float | Maximum HP |
| `ai_is_dead` | Damage Receiver | bool | True when HP ≤ 0 |
| `ai_took_damage` | Damage Receiver | bool | True the frame damage arrived |
| `ai_recent_damage` | Damage Receiver | bool | Alias for `ai_took_damage` |
| `ai_state` | AI State Machine | str | Current SM state |
| `ai_prev_state` | AI State Machine | str | Previous state |
| `ai_state_time` | AI State Machine | float | Seconds in current state |
| `ai_alert_level` | AI State Machine | float | 0–1 for HUD/shader use |
| `ai_attack_requested` | AI State Machine | bool | True when state == ATTACK |
| `ai_reload_requested` | AI State Machine | bool | True when no ammo and not DEAD |
| `ai_move_target` | AI State Machine / Navigation Follow Path | Vector\|None | Navigation destination |
| `ai_in_attack_range` | Enemy Combat | bool | Distance ≤ Attack Range |
| `ai_is_moving` | Enemy Movement | bool | Speed > 0.1 |
| `ai_nav_path` | Navigation Pathfinder | list[Vector] | Current BFS path |
| `ai_nav_index` | Navigation Pathfinder / Follow Path | int | Current waypoint index |
| `ai_reached_destination` | Navigation Follow Path | bool | Path completed |

---

## Damage channels (globalDict)

| Channel | Written by | Read by |
|---------|-----------|---------|
| `_dmg_<name>` | Enemy Combat, Player Hitscan | Damage Receiver |
| `_ai_dmg_<name>` | Enemy AI (melee/ranged) | Enemy AI (internal) |
| `_ai_dmg_src_<name>` | Enemy AI (knockback) | Enemy AI (internal) |

> **Important:** The `_dmg_*` and `_ai_dmg_*` channels are separate. The modular system uses `_dmg_*`; the Enemy AI prefab uses `_ai_dmg_*` for its internal system.

---

## Nodes in this category

### Primitive movement

| Node | Type | Description |
|------|------|-------------|
| [Seek](seek.md) | Exec | Chase a target with smooth arrival braking |
| [Flee](flee.md) | Exec | Flee from a target until safe radius |
| [Patrol](patrol.md) | Exec | Patrol between 2–8 waypoints |
| [Wander](wander.md) | Exec | Move in a periodically random direction |

### Detection

| Node | Type | Description |
|------|------|-------------|
| [Distance Check](distance-check.md) | Branch (Near/Far) | Branch by distance to target |
| [Line Of Sight](line-of-sight.md) | Branch (Visible/Hidden) | Raycast line-of-sight check |

### Modular system

| Node | Exec Order | Description |
|------|-----------|-------------|
| [Enemy Perception](enemy-perception.md) | 40 | FOV + raycast + memory |
| [Damage Receiver](damage-receiver.md) | 42 | HP and damage channel |
| [AI State Machine](ai-state-machine.md) | 50 | State machine IDLE→DEAD |
| [Enemy Decision](enemy-decision.md) | 51 | Branch by `ai_can_see` |
| [Navigation Pathfinder](navigation-pathfinder.md) | 52 | BFS over waypoints |
| [Navigation Follow Path](navigation-follow-path.md) | 54 | Follow path, stuck detection |
| [Enemy Movement](enemy-movement.md) | 55 | Smoothed movement |
| [Enemy Rotation](enemy-rotation.md) | 56 | Smooth rotation toward target |
| [Enemy Combat](enemy-combat.md) | 58 | Attack with cooldown |
| [Enemy Animation](enemy-animation.md) | 60 | State-driven animations |
| [Enemy Hit Flash](enemy-hit-flash.md) | 62 | Color flash on damage |

### Prefab

| Node | Description |
|------|-------------|
| [Enemy AI](enemy-ai.md) | All-in-one: states, detection, movement, combat, animations |
