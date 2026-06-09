# BT Service

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Resultado:** siempre `RUNNING`

Ejecuta código Python a intervalos regulares (no cada frame). Útil para tareas de actualización periódica que serían costosas cada frame: buscar enemigos, recalcular rutas, actualizar datos del blackboard con polling.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Interval (s) | Float (≥0.016) | `0.5` | Segundos entre ejecuciones del código |
| Code | String | `"self._bt_bb['service_tick'] = True"` | Python ejecutado cada Interval segundos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

Cada frame:
1. Acumula `deltaTime()` en un timer interno (`_btsvc_<sid>`).
2. Si el timer ≥ Interval: ejecuta `Code` y reinicia el timer a 0.
3. Siempre fija `_bt_last_result = 'RUNNING'`.
4. Continúa por `Out`.

El código **no se ejecuta el primer frame** — el timer debe acumular hasta Interval primero.

## Variables disponibles en `Code`

Idénticas a BTCustomTask: `self`, `self.own`, `self._bt_bb`, `Range`, `mathutils`.

## Diferencia con BT Simple Parallel y BT Custom Task

| Nodo | Frecuencia | Resultado |
|------|------------|-----------|
| BT Simple Parallel | Cada frame | `RUNNING` |
| BT Service | Cada N segundos | `RUNNING` |
| BT Custom Task | Cada frame | Configurable |

## Uso típico

### Buscar al jugador cada 0.5 segundos

```
Interval: 0.5
Code:
  player = self.own.scene.objects.get('Player')
  if player:
      diff = player.worldPosition - self.own.worldPosition
      self._bt_bb['enemy_dist']    = diff.length
      self._bt_bb['enemy_visible'] = diff.length < 20.0
  else:
      self._bt_bb['enemy_visible'] = False
```

### Actualizar waypoint de patrulla cada 2 segundos

```
Interval: 2.0
Code:
  idx = (self._bt_bb.get('waypoint_idx', 0) + 1) % 4
  self._bt_bb['waypoint_idx'] = idx
  self._bt_bb['waypoint'] = 'Waypoint_' + str(idx)
```

### Detectar colisiones con un raycast periódico

```
Interval: 0.25
Code:
  fwd = self.own.worldOrientation.col[1]
  rc  = self.own.rayCast(self.own.worldPosition + fwd * 5.0, None, 5.0)
  self._bt_bb['obstacle_ahead'] = rc[0] is not None
```

## Posición en el grafo

Colócalo al inicio del árbol para que los datos estén actualizados antes de que las condiciones los lean:

```
[OnUpdate] → [BT Service: actualizar datos de percepción]
                    │
                    Out ──► [BT Condition: enemy_visible] → ...
```

## Notas

- El intervalo mínimo es `0.016s` (~1 frame a 60fps). Para intervalos por frame, usa **BTSimpleParallel** o **BTCustomTask**.
- Errores en `Code` se imprimen en consola pero no interrumpen el árbol — el nodo devuelve `RUNNING` de todas formas.
- El timer es por instancia de nodo. Múltiples BTService en el mismo árbol tienen timers independientes.
- Como siempre devuelve `RUNNING`, no lo coloques como único nodo antes de un Selector — el Selector siempre irá a Failure. Usa el socket `Out` para continuar el árbol.
