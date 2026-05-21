# Inventory Manager

**Type:** Branch (In → Changed / Idle)  
**Category:** PLAYER

Manages up to 10 weapon inventory slots. Reads events from **Player Input** (`_pi_fire`, `_pi_reload`, `_pi_slot_changed`) and processes them to update ammo state and the active slot.

Branches to `Changed` when an event occurs (fire, reload, slot change) or to `Idle` otherwise.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Slot Count | Int (1–10) | `10` | Number of active slots |
| Slot [n] — Enable | Bool | `False` | Enables slot n |
| Slot [n] — Type | String | `""` | Weapon type identifier (e.g. `PISTOL`, `RIFLE`) |
| Slot [n] — Melee | Bool | `False` | Melee weapon (no ammo) |
| Slot [n] — Ammo | Int | `30` | Starting bullets in magazine |
| Slot [n] — Mag Size | Int | `30` | Maximum magazine capacity |
| Slot [n] — Magazines | Int | `5` | Reserve magazines |
| Slot [n] — Max Mags | Int | `5` | Maximum reserve magazines |
| Debug | Bool | `False` | Prints inventory state to console on each event |

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Changed | Output | Exec | When an event occurs |
| Idle | Output | Exec | When there are no events |

## Events and last event (`_inv_last_evt`)

| Event | Condition | `_inv_last_evt` value |
|-------|-----------|----------------------|
| Slot Changed | `_pi_slot_changed` | `"SLOT_CHANGE"` |
| Fired | `_pi_fire` with bullets available | `"FIRED"` |
| Empty magazine | `_pi_fire` with no bullets | `"EMPTY"` |
| Reloaded | `_pi_reload` with magazines available | `"RELOADED"` |
| No ammo | `_pi_reload` with no magazines | `"NO_AMMO"` |
| No event | — | `"NONE"` |

## Published state (`_inv_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `_inv_slots` | list[dict] | List of dicts with each slot's state |
| `_inv_active` | int | Active slot index (0–9) |
| `_inv_last_evt` | string | Last event that occurred this frame |
| `_inv_changed` | bool | `True` if any event occurred this frame |

Each dict in `_inv_slots` contains: `exists`, `type`, `melee`, `ammo_in_mag`, `ammo_max`, `magazines`, `mag_max`.

## Save/load integration

On the first frame, attempts to restore ammo and active weapon type from the BGE properties `_sl_inventory` and `_sl_weapon_state` on the object.

## Example graph

```
[Player Input] → [Weapon Fire Executor] → [Inventory Manager]
                                               ├── Changed ──► [Weapon State Publisher] → ...
                                               └── Idle    ──► (continues)
```

!!! warning "Requires Player Input on the same object"
    Reads `_pi_fire`, `_pi_reload` and `_pi_slot_changed` directly.
