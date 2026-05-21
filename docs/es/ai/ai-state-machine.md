# AI State Machine

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 50 (después de Perception y DamageReceiver)

Máquina de estados finitos para IA de enemigos. Lee las variables `ai_*` de percepción y combate, y decide el estado activo. Publica señales de combate y navegación para los nodos siguientes.

## Estados

```
DEAD (terminal)
    ↑
RETREAT ←→ ATTACK
              ↑↓
            CHASE
              ↑
            ALERT ← SEARCH
              ↑         ↑
       IDLE / PATROL ───┘
```

### Prioridad (mayor → menor)

1. **DEAD** — HP ≤ 0, terminal, nunca sale
2. **RETREAT** — HP bajo o sin munición; espera al menos `retreat_min` segundos
3. **ATTACK** — en rango, con LOS y munición
4. **CHASE** — target visible pero fuera de rango
5. **ALERT** — primera vista del target (pausa breve antes de CHASE)
6. **SEARCH** — target perdido de vista, va a última posición conocida
7. **IDLE / PATROL** — sin percepción del target

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Initial State | Enum | `IDLE` | Estado al crear el objeto |
| Alert Duration | Float (≥0.1) | `1.0` | Segundos en ALERT antes de confirmar CHASE |
| Search Duration | Float (≥0.1) | `5.0` | Segundos en SEARCH antes de rendirse y volver a IDLE |
| Retreat Min | Float (≥0.0) | `3.0` | Mínimo de segundos en RETREAT antes de re-evaluar |
| Low Health | Float (0–1) | `0.25` | Fracción de HP que activa RETREAT |
| Transition CD | Float (≥0.0) | `0.1` | Segundos mínimos entre cambios de estado |
| Debug | Bool | `False` | Imprimir transiciones por consola |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas (via `getattr`)

| Variable | Fuente | Descripción |
|----------|--------|-------------|
| `ai_can_see` | Enemy Perception | Target visible |
| `ai_in_attack_range` | Enemy Combat | En rango de ataque |
| `ai_has_ammo` | externo o True | Tiene munición |
| `ai_hp` | Damage Receiver | HP actual |
| `ai_hp_max` | Damage Receiver | HP máximo |
| `ai_last_seen_pos` | Enemy Perception | Última posición conocida |
| `ai_target` | Enemy Perception | Objeto target |
| `ai_is_dead` | Damage Receiver | Muerto |

Todas se leen con `getattr(self, 'ai_*', default)` — si un nodo fuente no está presente, se usan valores seguros.

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_state` | str | Estado actual (`'IDLE'`, `'CHASE'`, etc.) |
| `self.ai_prev_state` | str | Estado anterior |
| `self.ai_state_time` | float | Segundos en el estado actual |
| `self.ai_alert_level` | float | 0–1 para HUD / shaders |
| `self.ai_attack_requested` | bool | True cuando estado == ATTACK |
| `self.ai_reload_requested` | bool | True cuando sin ammo y no DEAD |
| `self.ai_move_target` | Vector\|None | Posición de destino para navegación |
| `self.own['ai_state']` | str | Propiedad BGE (para animaciones / lógica nativa) |

### Tabla de `ai_alert_level`

| Estado | Nivel |
|--------|-------|
| IDLE, PATROL | 0.0 |
| SEARCH | 0.3 |
| RETREAT | 0.5 |
| ALERT | 0.6 |
| CHASE | 0.8 |
| ATTACK | 1.0 |
| DEAD | 0.0 |

## Lógica de `ai_move_target`

| Estado | `ai_move_target` |
|--------|-----------------|
| CHASE | `ai_target_pos` o `ai_last_seen_pos` |
| SEARCH | `ai_last_seen_pos` |
| Otros | None |

Navigation Pathfinder lee `ai_move_target` para calcular el path.

## Uso típico

### Configuración defensiva (guerrero de baja salud)

```
Low Health: 0.4
Retreat Min: 5.0
Alert Duration: 0.5
Search Duration: 8.0
```

### Agresivo (jamás retrocede)

```
Low Health: 0.0  (nunca entra en RETREAT por HP)
```

### Pipeline completo

```
[OnUpdate]
    → [Enemy Perception]       (publica ai_can_see, ai_dist, ...)
    → [Damage Receiver]        (publica ai_hp, ai_is_dead, ...)
    → [AI State Machine]       (decide estado, publica ai_state, ai_move_target, ...)
    → [Navigation Pathfinder]  (calcula path)
    → [Navigation Follow Path] (sigue el path)
    → [Enemy Movement]         (mueve hacia ai_move_target)
    → [Enemy Rotation]         (rota hacia target)
    → [Enemy Combat]           (ataca si ATTACK)
    → [Enemy Animation]        (animación por estado)
    → [Enemy Hit Flash]        (flash visual)
```

## Notas

- DEAD es **terminal** — una vez muerto, `_aism_state_locked = True` y el SM nunca más ejecuta transiciones.
- ALERT funciona como buffer: evita que un enemigo pase de IDLE a CHASE instantáneamente con una sola muestra de visión. El timer de alerta se reinicia en cada entrada a ALERT.
- `transition_cooldown` evita el flickering de estados (cambiar ATTACK↔CHASE muy rápidamente). Valor 0 = sin cooldown.
- La propiedad BGE `self.own['ai_state']` permite que nodos nativos (animaciones, lógica de bricks) reaccionen al estado sin código Python.
