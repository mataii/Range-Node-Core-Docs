# AI State Machine

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 50 (after Perception and DamageReceiver)

Finite state machine for enemy AI. Reads `ai_*` perception and combat variables, decides the active state, and publishes combat signals and navigation hints for downstream nodes.

## States

```
DEAD (terminal)
    ↑
RETREAT ←→ ATTACK
              ↑↓
            CHASE
              ↑
            ALERT ← SEARCH
              ↑         ↑
       IDLE / PATROL ───┘
```

### Priority (highest → lowest)

1. **DEAD** — HP ≤ 0, terminal, never leaves
2. **RETREAT** — low HP or no ammo; stays at least `retreat_min` seconds
3. **ATTACK** — in range, with LOS and ammo
4. **CHASE** — target visible but out of range
5. **ALERT** — first sight of target (brief pause before CHASE)
6. **SEARCH** — target lost, heading to last known position
7. **IDLE / PATROL** — no target perception

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Initial State | Enum | `IDLE` | State when the object is created |
| Alert Duration | Float (≥0.1) | `1.0` | Seconds in ALERT before committing to CHASE |
| Search Duration | Float (≥0.1) | `5.0` | Seconds in SEARCH before giving up and returning to IDLE |
| Retreat Min | Float (≥0.0) | `3.0` | Minimum seconds in RETREAT before re-evaluating |
| Low Health | Float (0–1) | `0.25` | HP fraction that triggers RETREAT |
| Transition CD | Float (≥0.0) | `0.1` | Minimum seconds between state changes |
| Debug | Bool | `False` | Print state transitions to console |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read (via `getattr`)

| Variable | Source | Description |
|----------|--------|-------------|
| `ai_can_see` | Enemy Perception | Target visible |
| `ai_in_attack_range` | Enemy Combat | Within attack range |
| `ai_has_ammo` | external or True | Has ammo |
| `ai_hp` | Damage Receiver | Current HP |
| `ai_hp_max` | Damage Receiver | Maximum HP |
| `ai_last_seen_pos` | Enemy Perception | Last known position |
| `ai_target` | Enemy Perception | Target object |
| `ai_is_dead` | Damage Receiver | Is dead |

All read with `getattr(self, 'ai_*', default)` — if a source node is absent, safe defaults are used.

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_state` | str | Current state (`'IDLE'`, `'CHASE'`, etc.) |
| `self.ai_prev_state` | str | Previous state |
| `self.ai_state_time` | float | Seconds in the current state |
| `self.ai_alert_level` | float | 0–1 for HUD/shader use |
| `self.ai_attack_requested` | bool | True when state == ATTACK |
| `self.ai_reload_requested` | bool | True when out of ammo and not DEAD |
| `self.ai_move_target` | Vector\|None | Navigation destination |
| `self.own['ai_state']` | str | BGE property (for animations / native logic) |

### `ai_alert_level` table

| State | Level |
|-------|-------|
| IDLE, PATROL | 0.0 |
| SEARCH | 0.3 |
| RETREAT | 0.5 |
| ALERT | 0.6 |
| CHASE | 0.8 |
| ATTACK | 1.0 |
| DEAD | 0.0 |

## `ai_move_target` logic

| State | `ai_move_target` |
|-------|-----------------|
| CHASE | `ai_target_pos` or `ai_last_seen_pos` |
| SEARCH | `ai_last_seen_pos` |
| Others | None |

Navigation Pathfinder reads `ai_move_target` to compute the path.

## Typical usage

### Defensive configuration (low-health warrior)

```
Low Health: 0.4
Retreat Min: 5.0
Alert Duration: 0.5
Search Duration: 8.0
```

### Aggressive (never retreats)

```
Low Health: 0.0  (never enters RETREAT due to HP)
```

### Full pipeline

```
[OnUpdate]
    → [Enemy Perception]        (publishes ai_can_see, ai_dist, ...)
    → [Damage Receiver]         (publishes ai_hp, ai_is_dead, ...)
    → [AI State Machine]        (decides state, publishes ai_state, ai_move_target, ...)
    → [Navigation Pathfinder]   (computes path)
    → [Navigation Follow Path]  (follows path)
    → [Enemy Movement]          (moves toward ai_move_target)
    → [Enemy Rotation]          (rotates toward target)
    → [Enemy Combat]            (attacks when ATTACK)
    → [Enemy Animation]         (animation by state)
    → [Enemy Hit Flash]         (visual flash)
```

## Notes

- DEAD is **terminal** — once dead, `_aism_state_locked = True` and the SM never executes transitions again.
- ALERT acts as a buffer: prevents an enemy from jumping from IDLE to CHASE instantly on a single frame of visibility. The alert timer resets on each entry to ALERT.
- `transition_cooldown` prevents state flickering (rapidly switching ATTACK↔CHASE). Value 0 = no cooldown.
- The BGE property `self.own['ai_state']` lets native nodes (animations, brick logic) react to the state without Python code.
