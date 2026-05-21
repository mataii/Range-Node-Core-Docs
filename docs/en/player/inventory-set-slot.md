# Inventory Set Slot

**Type:** Exec (In → Out)  
**Category:** PLAYER

Modifies an inventory slot at runtime. Use it for weapon pickups, drops, or any dynamic inventory change without recompiling the graph.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Slot | Int (0–9) | `0` | Slot index to modify (overridden by socket) |
| Enable | Bool | `True` | `True` = give weapon, `False` = remove weapon |
| Type | String | `"NONE"` | Weapon type (e.g. `PISTOL`, `RIFLE`, `KNIFE`) |
| Melee | Bool | `False` | If enabled, ignores all ammo properties |
| Ammo | Int | `30` | Starting bullets in magazine |
| Mag Size | Int | `30` | Maximum magazine capacity |
| Magazines | Int | `3` | Starting reserve magazines |
| Max Mags | Int | `5` | Maximum reserve magazines |

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Slot | Input | Data | Slot index (overrides the Slot property) |
| Out | Output | Exec | |

## Behavior

- If `Enable = True`: the slot receives the configured values.
- If `Enable = False`: the slot is cleared (`exists = False`, type `"NONE"`, ammo set to 0).
- If the index is out of the `_inv_slots` range, the operation is silently ignored.

## Typical usage

```
[Trigger: Pickup Pistol] → [Inventory Set Slot: Slot=0, Type="PISTOL", Enable=True] → [Out]
[Trigger: Drop Weapon]   → [Inventory Set Slot: Enable=False] → [Out]
```

!!! warning "Requires Inventory Manager on the same object"
    Directly modifies `_inv_slots`. Without Inventory Manager, the list does not exist and the node does nothing.
