# Weapon FX System

**Type:** Exec (In → Out)  
**Category:** PLAYER

Modern weapon visual effects system. Reactive to pipeline events — spawns FX objects for muzzle flash, smoke, shell ejection, and impact. Supports surface-differentiated FX when used alongside **Surface Material**.

## Properties

### Muzzle flash

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Muzzle Flash | Bool | `True` | Enables muzzle flash |
| Muzzle Object | String | `"MuzzleFX"` | Inactive scene object |
| Muzzle Duration | Float | `0.05` | Seconds `weapon_muzzle_active` stays `True` after a shot |
| Muzzle Frames | Int | `3` | Spawned object lifetime in frames |
| Suppress in ADS | Bool | `True` | Do not spawn flash while aiming |

### Smoke

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Smoke FX | Bool | `False` | Enables barrel smoke |
| Smoke Object | String | `"SmokeFX"` | Inactive scene object |
| Smoke Frames | Int | `60` | Spawned object lifetime in frames |

### Shell ejection

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Shell Eject | Bool | `False` | Enables shell ejection |
| Shell Object | String | `"ShellFX"` | Inactive scene object |
| Shell Frames | Int | `120` | Spawned object lifetime in frames |
| Shell Delay | Float | `0.05` | Seconds after shot before ejecting the shell |

### Impact

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Impact FX | Bool | `True` | Enables impact FX |
| Impact Object | String | `"ImpactFX"` | Default inactive object |
| Impact Duration | Float | `0.1` | Seconds `weapon_impact_active` stays `True` after a hit |
| Impact Frames | Int | `30` | Spawned object lifetime in frames |
| Surface FX | Bool | `False` | Uses different FX per surface type (requires Surface Material) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_muzzle_active` | bool | `True` for `Muzzle Duration` after a shot |
| `weapon_impact_active` | bool | `True` for `Impact Duration` after a hit |
| `weapon_fx_active` | bool | `True` if muzzle or impact are active |

## Graph position

```
[Hit Scan Weapon / Weapon Projectile] → [Weapon FX System]
```

All FX objects must exist as **inactive** in the scene before the game starts.

## Surface Material integration

When `Surface FX = True`, reads `surface_fx_name` and `surface_audio_name` published by **Surface Material** to select the correct impact object based on the surface type hit.
