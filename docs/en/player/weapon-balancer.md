# Weapon Balancer

**Type:** Exec (In → Out)  
**Category:** PLAYER

Applies per-weapon-class multipliers to pipeline parameters. Defines a stats table (Pistol, Rifle, Shotgun, Sniper, SMG, etc.) and applies them automatically based on `weapon_type`, publishing the `wb_*` multipliers that other nodes consume.

## Properties

### Global caps

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Damage Cap | Float (0.1–20) | `5.0` | Maximum damage multiplier |
| Recoil Cap | Float (0.1–20) | `5.0` | Maximum recoil multiplier |
| Spread Cap | Float (0.1–20) | `5.0` | Maximum spread multiplier |
| Fire Rate Cap | Float (0.1–20) | `5.0` | Maximum fire rate multiplier |
| Fallback | String | `"rifle"` | Class used when `weapon_type` is not in the table |
| Debug | Bool | `False` | Prints active multipliers to the console |

### Global modifiers

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| ADS Spread × | Float (0–1) | `0.60` | Spread when ADS is active (< 1 = more accurate) |
| ADS Recoil × | Float (0–1) | `0.80` | Recoil when ADS is active |
| Hipfire × | Float (≥1) | `1.30` | Spread penalty when not aiming |
| Move × | Float (≥1) | `1.40` | Spread penalty while moving |

### Per-class stats (D=Damage, R=Recoil, S=Spread, F=Fire Rate)

| Class | D | R | S | F |
|-------|---|---|---|---|
| Pistol | 0.75 | 0.60 | 1.10 | 1.40 |
| Rifle | 1.00 | 1.00 | 1.00 | 1.00 |
| Shotgun | 1.40 | 1.70 | 2.50 | 0.55 |
| Sniper | 2.50 | 2.00 | 0.30 | 0.20 |
| SMG | 0.50 | 0.70 | 1.30 | 2.00 |
| LMG | 0.90 | 1.40 | 1.60 | 1.80 |
| Melee | 1.00 | 0.00 | 0.00 | 1.00 |

*(All values are editable by default)*

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables (`wb_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `wb_damage` | float | Damage multiplier |
| `wb_recoil` | float | Recoil multiplier |
| `wb_spread` | float | Spread multiplier |
| `wb_fire_rate` | float | Fire rate multiplier |
| `wb_class` | string | Active weapon class |

## Graph position

```
[Weapon State Publisher] → [Weapon Balancer] → [Weapon Reload] → [Weapon Fire Executor] → ...
```

Must come **before** Fire Executor and damage nodes so the multipliers are available.
