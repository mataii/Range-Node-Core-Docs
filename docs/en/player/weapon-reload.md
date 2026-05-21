# Weapon Reload

**Type:** Exec (In → Out)  
**Category:** PLAYER

Adds a timed reload on top of **Inventory Manager** events. Blocks firing during the reload by publishing `weapon_is_reloading = True`.

Must be placed **before** Weapon Fire Executor so the block is visible to it.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Reload Time | Float | `2.0` | Reload duration in seconds |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_is_reloading` | bool | `True` throughout the entire reload |
| `weapon_reload_progress` | float (0.0–1.0) | Reload progress (useful for HUD/animation) |
| `weapon_reload_finished` | bool | One-frame pulse when reload completes |

## Flow

```
frame
  │
  ├── If _wr_active:
  │       timer -= dt
  │       If timer <= 0:
  │           is_reloading = False
  │           reload_finished = True (one frame)
  │           progress = 1.0
  │       Else:
  │           is_reloading = True
  │           progress = 1 - (timer / reload_time)
  │
  └── If weapon_reload_requested and not _wr_active:
          _wr_active = True
          timer = reload_time
          is_reloading = True
```

## Graph position

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon Fire Executor] → ...
                                ↓
                      [Weapon Animation]  ← reads weapon_is_reloading, weapon_reload_finished
```

!!! info "weapon_reload_requested"
    This flag is published by Weapon State Publisher when `_inv_last_evt == "RELOADED"`. Inventory Manager already validated that magazines are available before publishing it.
