# Player Jump

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Aplica salto al objeto cuando el jugador presiona la tecla configurada en **Player Input**, siempre que el objeto esté sobre el suelo. Soporta doble salto y ambos modos de física de Range.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Physics | Enum | `Character` | Modo de física del objeto |
| Jump Force | Float | `7.0` | Fuerza / velocidad del salto |
| Jump Frames | Int (1–60) | `10` | Frames de movimiento ascendente (solo modo Character) |
| Max Jumps | Int (1–10) | `1` | 1 = salto normal, 2 = doble salto, etc. |
| Ground Dist | Float | `0.6` | Distancia del raycast hacia abajo para detectar suelo. Ajustar a: `altura_objeto / 2 + 0.1` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Modos de física

### Character
Aplica `applyMovement` vertical durante `Jump Frames` frames:

```python
self.own.applyMovement((0.0, 0.0, jump_force * dt), False)
```

Cada frame del timer aplica un impulso hacia arriba en espacio mundo.

### Dynamic
Asigna `localLinearVelocity.z` directamente en el frame del salto:

```python
vel = self.own.localLinearVelocity.copy()
vel.z = jump_force
self.own.localLinearVelocity = vel
```

## Detección de suelo

El nodo lanza un raycast hacia abajo desde `worldPosition` hasta una distancia de `Ground Dist`. Si el raycast no impacta ningún objeto, el flag `_pj_in_air` se activa. Al tocar suelo de nuevo, se restauran los saltos disponibles.

## Estado publicado (`_pj_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_pj_in_air` | bool | `True` mientras el objeto no está en contacto con el suelo |
| `_pj_timer` | int | Frames restantes de impulso ascendente (solo Character) |
| `_pj_jumps_left` | int | Saltos restantes antes de necesitar tocar suelo |

Estos valores son leídos por **Player Movement** (air control) y **Player State** (estado JUMPING / FALLING).

## Configuración de Ground Dist

Un valor incorrecto de `Ground Dist` provoca que el objeto no detecte el suelo o que nunca lo pierda:

| Altura del objeto | Ground Dist recomendado |
|-------------------|------------------------|
| 1.8 u (humano) | 0.95–1.0 |
| 1.0 u | 0.55–0.6 |
| 0.5 u | 0.3–0.35 |

!!! warning "Requiere Player Input en el mismo objeto"
    Lee `_pi_jump` para saber cuándo saltar.

## Grafo de ejemplo

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → ...
```

Player Jump debe ir **antes** de Player Movement en el grafo para que `_pj_in_air` esté disponible cuando Player Movement lo lea.
