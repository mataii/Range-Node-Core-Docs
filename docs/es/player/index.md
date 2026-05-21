# Categoría PLAYER

La categoría **PLAYER** contiene todos los nodos necesarios para construir un personaje jugable completo: desde la lectura de teclado hasta el sistema de armas, inventario, salud y guardado.

## Pipeline típico de un FPS

```
[OnUpdate]
    │
    ▼
[Player Input]          ← lee teclado y mouse cada frame
    │
    ├──► [Player Jump]  ← aplica salto con física
    │
    ├──► [Player Movement] ← aplica movimiento WASD
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

## Grupos de nodos

### Entrada
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Player Input](player-input.md) | Exec | Lee teclado + mouse, publica `_pi_*` |
| [Player Input Data](player-input-data.md) | Data | Lee un valor `_pi_*` como dato |
| [Player State](player-state.md) | Data | Estado del jugador: IDLE / WALKING / JUMPING / FALLING |

### Movimiento
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Player Movement](player-movement.md) | Exec | Aplica movimiento WASD con sprint, aceleración y control aéreo |
| [Player Jump](player-jump.md) | Exec | Salto con física CHARACTER o DYNAMIC; soporta doble salto |

### Salud
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Player Health](player-health.md) | Branch | Recibe daño, regenera HP; branch `Alive / Dead` |
| [Player Health State](player-health-state.md) | Data | HP actual, máximo, porcentaje |

### Inventario
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Inventory Manager](inventory-manager.md) | Branch | Gestiona slots de armas; branch `Changed / Idle` |
| [Inventory State](inventory-state.md) | Data | Lee estado del arma activa |
| [Inventory Set Slot](inventory-set-slot.md) | Exec | Modifica un slot en runtime (pickup, drop) |

### Pipeline de armas
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Weapon State Publisher](weapon-state-publisher.md) | Exec | Publica `weapon_*` normalizados desde el inventario |
| [Weapon Fire Executor](weapon-fire-executor.md) | Exec | Cadencia, modos Semi/Auto/Burst, publica `weapon_fired` |
| [Weapon Reload](weapon-reload.md) | Exec | Recarga con duración, bloquea el disparo durante recarga |
| [Weapon ADS](weapon-ads.md) | Exec | Apuntar (ADS): transición de alpha, FOV, retroceso, dispersión |
| [Weapon Recoil](weapon-recoil.md) | Exec | Acumula y recupera retroceso de cámara |
| [Weapon Spread](weapon-spread.md) | Exec | Cono de dispersión balística; bloom, patrón, penalizaciones |
| [Weapon Animation](weapon-animation.md) | Exec | Máquina de estados de animación para el modelo del arma |

### Daño
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Hit Scan Weapon](hit-scan-weapon.md) | Exec | Raycast instantáneo; soporta pellets (escopeta), trazadores |
| [Weapon Projectile](weapon-projectile.md) | Exec | Spawnea un objeto proyectil con dirección y velocidad |
| [Projectile System](projectile-system.md) | Exec | Comportamiento del proyectil: colisión, daño, gravedad, pool |
| [Explosion Damage](explosion-damage.md) | Exec | Daño radial con falloff; impulso opcional |

### Efectos
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Weapon FX System](weapon-fx-system.md) | Exec | Flash de cañón, humo, casquillos, impacto, FX por superficie |
| [Surface Material](surface-material.md) | Exec | Lee el tipo de superficie del objeto impactado y publica FX/audio |

### Sistemas
| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Object Pool](object-pool.md) | Exec | Pool de objetos pre-instanciados para reutilizar |
| [Interaction System](interaction-system.md) | Exec | Raycast de interacción (pulsar / mantener); envía mensajes |
| [Weapon Balancer](weapon-balancer.md) | Exec | Aplica multiplicadores por clase de arma a `weapon_*` |
| [Save Load System](save-load-system.md) | Exec | Guardado/carga de partida en JSON (HP, munición, mundo) |
| [HUD State Publisher](hud-state-publisher.md) | Exec | Publica `ui_*` para consumo de overlays o UI nodes |

### Obsoletos (Legacy)
Estos nodos están marcados como **deprecated** en el editor. Úsalos solo para proyectos existentes.

| Nodo | Reemplazado por |
|------|-----------------|
| Weapon Fire | Weapon Fire Executor |
| Weapon Hitscan | Hit Scan Weapon |
| Weapon FX | Weapon FX System |
| Projectile Behavior | Projectile System |

## Convenciones de variables compartidas

Todos los nodos PLAYER se comunican a través de variables de instancia en `self`:

| Prefijo | Propietario | Descripción |
|---------|-------------|-------------|
| `_pi_*` | Player Input | Estado de teclado/mouse normalizado |
| `_pj_*` | Player Jump | Estado de salto y contacto con suelo |
| `_pm_*` | Player Movement | Velocidad interna suavizada |
| `_ph_*` | Player Health | HP y timer de regeneración |
| `_inv_*` | Inventory Manager | Slots y evento activo |
| `weapon_*` | Weapon State Publisher | Estado normalizado del arma activa |
