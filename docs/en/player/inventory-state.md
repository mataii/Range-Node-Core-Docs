# Inventory State

**Type:** Data (no exec sockets)  
**Category:** PLAYER

Pure data node. Reads the active inventory slot state stored by **Inventory Manager** on the same object.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| Data | Enum | Value to expose |

### Data selector options

| Option | Type | Description |
|--------|------|-------------|
| Weapon Type | string | Active weapon type (e.g. `"PISTOL"`, `"NONE"`) |
| Ammo In Mag | int | Bullets remaining in current magazine |
| Mag Size | int | Maximum magazine capacity |
| Magazines | int | Reserve magazines available |
| Max Magazines | int | Maximum reserve magazines |
| Active Slot | int | Active slot index (0–9) |
| Last Event | string | Last event: `NONE / FIRED / EMPTY / RELOADED / NO_AMMO / SLOT_CHANGE` |
| Exists | bool | `True` if the active slot has a weapon assigned |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data |

## Typical usage

```
[Inventory State: AMMO_IN_MAG] ──► [UI Text: Set]
[Inventory State: LAST_EVENT] ──► [Compare: == "FIRED"] ──► [Play Sound Gunshot]
[Inventory State: EXISTS] ──► [Branch] ──► [Show Crosshair]
```

!!! info "Requires Inventory Manager on the same object"
    Without Inventory Manager, returns default values (`"NONE"`, 0, `False`).
