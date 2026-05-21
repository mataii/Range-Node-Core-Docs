# Weapon Fire Executor

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Controla la cadencia de disparo y el modo de fuego. Publica `weapon_fired = True` en el frame exacto en que se produce un disparo válido. Es la fuente autoritativa del evento de disparo — todos los nodos posteriores (daño, FX, animación, retroceso) deben leer `weapon_fired`.

Debe colocarse **después** de Weapon State Publisher y **antes** de los nodos de daño.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Mode | Enum | `Semi-Auto` | Modo de disparo |
| Fire Rate | Float (0.1–100) | `10.0` | Disparos por segundo (Auto y Burst) |
| Burst Count | Int (2–20) | `3` | Disparos por ráfaga (solo modo Burst) |

### Modos de disparo

| Modo | Comportamiento |
|------|----------------|
| `Semi-Auto` | Un disparo por pulsación. Ignora el botón mantenido. |
| `Auto` | Disparo continuo mientras se mantiene el botón y el cooldown permite. |
| `Burst` | Dispara `Burst Count` rondas por pulsación a la cadencia configurada. |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas (`weapon_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_fired` | bool | `True` solo el frame en que se produce un disparo |
| `weapon_fire_mode` | string | Modo activo: `"SEMI"` / `"AUTO"` / `"BURST"` |
| `weapon_fire_cooldown` | float | Tiempo restante del cooldown (segundos) |
| `weapon_trigger_held` | bool | `True` mientras el gatillo esté activo |
| `weapon_burst_remaining` | int | Rondas restantes en la ráfaga actual |
| `weapon_is_reloading` | bool | Leído de Weapon Reload — bloquea el disparo |

## Condiciones de disparo

Para que `weapon_fired = True` se cumplan las siguientes condiciones simultáneamente:

1. `weapon_can_fire = True` (publicado por Weapon State Publisher)
2. `weapon_is_reloading = False` (publicado por Weapon Reload)
3. Cooldown `<= 0`
4. Gatillo activo según el modo

## Integración con Weapon Balancer

El cooldown efectivo se divide por `wb_fire_rate` (publicado por Weapon Balancer). Si Weapon Balancer no está presente, el divisor es 1.0 (sin efecto).

## Posición en el grafo

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon Fire Executor] → [Weapon Spread] → [Hit Scan Weapon / Weapon Projectile]
                                                                                ↓
                                                                         [Weapon Recoil]
                                                                         [Weapon Animation]
```
