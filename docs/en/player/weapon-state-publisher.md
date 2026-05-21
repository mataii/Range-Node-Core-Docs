# Weapon State Publisher

**Type:** Exec (In → Out)  
**Category:** PLAYER

Normalizing bridge between **Inventory Manager** and the rest of the weapon pipeline. Reads the active slot from `_inv_slots` and publishes all `weapon_*` variables in a standard format consumed by downstream nodes.

Must be placed **after** Inventory Manager in the graph.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables (`weapon_*`)

These variables are **read-only** for all nodes except this one.

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_exists` | bool | The active slot has a weapon |
| `weapon_type` | string | Active weapon type (e.g. `"PISTOL"`) |
| `weapon_is_melee` | bool | The active weapon is melee |
| `weapon_ammo` | int | Bullets in the current magazine |
| `weapon_ammo_max` | int | Maximum magazine capacity |
| `weapon_magazines` | int | Reserve magazines |
| `weapon_magazines_max` | int | Maximum reserve magazines |
| `weapon_slot` | int | Active slot index |
| `weapon_can_fire` | bool | `True` if weapon exists AND (is melee OR has ammo) |
| `weapon_fire_requested` | bool | `True` on the frame `_inv_last_evt == "FIRED"` |
| `weapon_reload_requested` | bool | `True` on the frame `_inv_last_evt == "RELOADED"` |

## Graph position

```
[Inventory Manager]
    ├── Changed ──► [Weapon State Publisher] → [Weapon Fire Executor] → ...
    └── Idle    ──►
```

!!! note "Why this node exists"
    Inventory Manager works with Python dicts (`_inv_slots[n]`). This node converts those dicts to flat typed variables (`weapon_ammo: int`), making the rest of the pipeline independent of the inventory's internal implementation.
