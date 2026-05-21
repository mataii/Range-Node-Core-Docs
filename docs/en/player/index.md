# PLAYER Category

The **PLAYER** category contains all the nodes needed to build a complete playable character: from keyboard input to the weapon system, inventory, health, and save/load.

## Typical FPS pipeline

```
[OnUpdate]
    │
    ▼
[Player Input]          ← reads keyboard and mouse every frame
    │
    ├──► [Player Jump]  ← applies jump physics
    │
    ├──► [Player Movement] ← applies WASD movement
    │
    ▼
[Inventory Manager] ─── Changed ──► [Weapon State Publisher]
        │                                    │
        └─── Idle ──►                        ▼
                               [Weapon Fire Executor]
                                       │
                          ┌────────────┼────────────┐
                          ▼            ▼             ▼
                    [Weapon Reload] [Weapon ADS] [Weapon Recoil]
                                                      │
                                               [Weapon Spread]
                                                      │
                                    ┌─────────────────┴──────────────────┐
                                    ▼                                     ▼
                             [Hit Scan Weapon]               [Weapon Projectile]
                                    │                                     │
                             [Weapon FX System]              [Projectile System]
```

## Node groups

### Input
| Node | Type | Description |
|------|------|-------------|
| [Player Input](player-input.md) | Exec | Reads keyboard + mouse, publishes `_pi_*` |
| [Player Input Data](player-input-data.md) | Data | Reads a `_pi_*` value as a data output |
| [Player State](player-state.md) | Data | Player state: IDLE / WALKING / JUMPING / FALLING |

### Movement
| Node | Type | Description |
|------|------|-------------|
| [Player Movement](player-movement.md) | Exec | Applies WASD movement with sprint, acceleration and air control |
| [Player Jump](player-jump.md) | Exec | Jump with CHARACTER or DYNAMIC physics; supports double jump |

### Health
| Node | Type | Description |
|------|------|-------------|
| [Player Health](player-health.md) | Branch | Receives damage, regenerates HP; branch `Alive / Dead` |
| [Player Health State](player-health-state.md) | Data | Current HP, max HP, percentage |

### Inventory
| Node | Type | Description |
|------|------|-------------|
| [Inventory Manager](inventory-manager.md) | Branch | Manages weapon slots; branch `Changed / Idle` |
| [Inventory State](inventory-state.md) | Data | Reads active weapon state |
| [Inventory Set Slot](inventory-set-slot.md) | Exec | Modifies a slot at runtime (pickup, drop) |

### Weapon Pipeline
| Node | Type | Description |
|------|------|-------------|
| [Weapon State Publisher](weapon-state-publisher.md) | Exec | Publishes normalized `weapon_*` from inventory |
| [Weapon Fire Executor](weapon-fire-executor.md) | Exec | Fire rate, Semi/Auto/Burst modes, publishes `weapon_fired` |
| [Weapon Reload](weapon-reload.md) | Exec | Timed reload, blocks firing during reload |
| [Weapon ADS](weapon-ads.md) | Exec | Aim Down Sights: alpha transition, FOV, recoil, spread |
| [Weapon Recoil](weapon-recoil.md) | Exec | Accumulates and recovers camera recoil |
| [Weapon Spread](weapon-spread.md) | Exec | Ballistic spread cone; bloom, pattern, penalties |
| [Weapon Animation](weapon-animation.md) | Exec | Priority-based animation state machine for the weapon model |

### Damage
| Node | Type | Description |
|------|------|-------------|
| [Hit Scan Weapon](hit-scan-weapon.md) | Exec | Instant raycast; supports pellets (shotgun), tracers |
| [Weapon Projectile](weapon-projectile.md) | Exec | Spawns a projectile object with direction and velocity |
| [Projectile System](projectile-system.md) | Exec | Projectile behavior: collision, damage, gravity, pool |
| [Explosion Damage](explosion-damage.md) | Exec | Radial damage with falloff; optional impulse |

### Effects
| Node | Type | Description |
|------|------|-------------|
| [Weapon FX System](weapon-fx-system.md) | Exec | Muzzle flash, smoke, shells, impact, surface FX |
| [Surface Material](surface-material.md) | Exec | Reads surface type from hit object and publishes FX/audio |

### Systems
| Node | Type | Description |
|------|------|-------------|
| [Object Pool](object-pool.md) | Exec | Pool of pre-instantiated objects for reuse |
| [Interaction System](interaction-system.md) | Exec | Interaction raycast (press / hold); sends messages |
| [Weapon Balancer](weapon-balancer.md) | Exec | Applies per-weapon-class multipliers to `weapon_*` |
| [Save Load System](save-load-system.md) | Exec | JSON save/load (HP, ammo, world) |
| [HUD State Publisher](hud-state-publisher.md) | Exec | Publishes `ui_*` for HUD/overlay consumers |

### Legacy (Deprecated)
These nodes are marked as **deprecated** in the editor. Use them only for existing projects.

| Node | Replaced by |
|------|-------------|
| Weapon Fire | Weapon Fire Executor |
| Weapon Hitscan | Hit Scan Weapon |
| Weapon FX | Weapon FX System |
| Projectile Behavior | Projectile System |

## Shared variable conventions

All PLAYER nodes communicate through instance variables on `self`:

| Prefix | Owner | Description |
|--------|-------|-------------|
| `_pi_*` | Player Input | Normalized keyboard/mouse state |
| `_pj_*` | Player Jump | Jump and ground contact state |
| `_pm_*` | Player Movement | Smoothed internal velocity |
| `_ph_*` | Player Health | HP and regen timer |
| `_inv_*` | Inventory Manager | Slots and active event |
| `weapon_*` | Weapon State Publisher | Normalized active weapon state |
