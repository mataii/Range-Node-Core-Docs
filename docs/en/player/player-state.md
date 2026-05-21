# Player State

**Type:** Data (no exec sockets)  
**Category:** PLAYER

Pure data node. Derives the player's movement state from `_pi_*` (Player Input) and `_pj_*` (Player Jump) flags. Useful for connecting to animations, UI, or game logic.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| Data | Enum | Value to expose (see table below) |

### Data selector options

| Option | Type | Description |
|--------|------|-------------|
| State | string | `"IDLE"` / `"WALKING"` / `"JUMPING"` / `"FALLING"` |
| Is Idle | bool | `True` when still and on the ground |
| Is Walking | bool | `True` when moving and on the ground |
| Is Jumping | bool | `True` while the jump timer is active (ascending) |
| Is Falling | bool | `True` when airborne but **not** ascending |
| Is Grounded | bool | `True` when on the ground |
| Is Airborne | bool | `True` when in the air |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data |

## Internal logic

| State | Condition |
|-------|-----------|
| JUMPING | `_pj_timer > 0` |
| FALLING | `_pj_in_air AND NOT _pj_timer > 0` |
| WALKING | `_pi_is_moving AND NOT _pj_in_air` |
| IDLE | any other case |

!!! warning "Requires Player Input + Player Jump on the same object"
    Without Player Jump, `_pj_in_air` and `_pj_timer` do not exist and all aerial states will return `False`.
