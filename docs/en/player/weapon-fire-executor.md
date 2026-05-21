# Weapon Fire Executor

**Type:** Exec (In → Out)  
**Category:** PLAYER

Controls fire rate and fire mode. Publishes `weapon_fired = True` on the exact frame a valid shot is produced. It is the authoritative source of the fire event — all downstream nodes (damage, FX, animation, recoil) must read `weapon_fired`.

Must be placed **after** Weapon State Publisher and **before** damage nodes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mode | Enum | `Semi-Auto` | Fire mode |
| Fire Rate | Float (0.1–100) | `10.0` | Rounds per second (Auto and Burst) |
| Burst Count | Int (2–20) | `3` | Rounds per burst (Burst mode only) |

### Fire modes

| Mode | Behavior |
|------|----------|
| `Semi-Auto` | One shot per press. Ignores held button. |
| `Auto` | Continuous fire while button is held and cooldown allows. |
| `Burst` | Fires `Burst Count` rounds per press at the configured rate. |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables (`weapon_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_fired` | bool | `True` only on the frame a shot is produced |
| `weapon_fire_mode` | string | Active mode: `"SEMI"` / `"AUTO"` / `"BURST"` |
| `weapon_fire_cooldown` | float | Remaining cooldown time (seconds) |
| `weapon_trigger_held` | bool | `True` while the trigger is active |
| `weapon_burst_remaining` | int | Rounds remaining in the current burst |
| `weapon_is_reloading` | bool | Read from Weapon Reload — blocks firing |

## Fire conditions

For `weapon_fired = True` all of the following conditions must be met simultaneously:

1. `weapon_can_fire = True` (published by Weapon State Publisher)
2. `weapon_is_reloading = False` (published by Weapon Reload)
3. Cooldown `<= 0`
4. Trigger active according to the mode

## Weapon Balancer integration

The effective cooldown is divided by `wb_fire_rate` (published by Weapon Balancer). If Weapon Balancer is not present, the divisor is 1.0 (no effect).

## Graph position

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon Fire Executor] → [Weapon Spread] → [Hit Scan Weapon / Weapon Projectile]
                                                                                ↓
                                                                         [Weapon Recoil]
                                                                         [Weapon Animation]
```
