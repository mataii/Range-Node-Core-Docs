# Weapon FX (Legacy)

**Type:** Exec (In → Out)  
**Category:** PLAYER

> **Deprecated.** For new projects use **Weapon FX System**, which adds support for smoke, shell ejection delay, and surface-based FX via Surface Material.

Spawns FX objects in response to `weapon_fired` (muzzle flash, shell) and `weapon_hit` (impact). Manages timers for the `weapon_muzzle_active` and `weapon_impact_active` state variables.

## Properties

### Muzzle flash

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Muzzle Flash | Bool | `True` | Enables muzzle flash |
| Muzzle Object | String | `"MuzzleFX"` | Name of the inactive scene object |
| Muzzle Duration | Float (≥0.01) | `0.05` | Seconds `weapon_muzzle_active` stays `True` after a shot |
| Muzzle Lifetime | Int (≥1) | `3` | Spawned object lifetime in frames |
| Suppress in ADS | Bool | `True` | Does not spawn flash while `weapon_ads_active = True` |

### Shell ejection

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Shell Eject | Bool | `False` | Enables shell ejection |
| Shell Object | String | `"ShellFX"` | Name of the inactive scene object |
| Shell Lifetime | Int (≥1) | `120` | Spawned object lifetime in frames |

### Impact

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Impact FX | Bool | `True` | Enables impact FX |
| Impact Object | String | `"ImpactFX"` | Name of the inactive scene object |
| Impact Duration | Float (≥0.01) | `0.1` | Seconds `weapon_impact_active` stays `True` after a hit |
| Impact Lifetime | Int (≥1) | `30` | Spawned object lifetime in frames |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_fx_active` | bool | `True` if muzzle or impact are active |
| `weapon_muzzle_active` | bool | `True` for `Muzzle Duration` after a shot |
| `weapon_impact_active` | bool | `True` for `Impact Duration` after a hit |

## Scene requirements

All FX objects must exist as **inactive** in the scene before the game starts. The node activates them with `scene.addObject()` passing the frame count as lifetime.

The impact object is automatically positioned at `weapon_hit_position` and aligned with `weapon_hit_normal` (requires Weapon Hitscan before it in the graph).

## Graph position

```
[Weapon Hitscan (Legacy)] → [Weapon FX (Legacy)]
```

## Migration to the new system

| Legacy | New system |
|--------|------------|
| Weapon FX (Legacy) | Weapon FX System |
| No smoke | Weapon FX System with `Smoke FX` |
| No shell delay | Weapon FX System with `Shell Delay` |
| No surface FX | Weapon FX System with `Surface FX` + Surface Material |
