# BT Wait

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Tarea**

Espera un tiempo fijo (o aleatorio) antes de reportar `SUCCESS`. Devuelve `RUNNING` en cada frame mientras el temporizador no expira.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Duration (s) | Float (≥0) | `1.0` | Segundos a esperar |
| Randomize | Bool | `False` | Aleatoriza la duración entre `Duration` y `Duration + Variance` |
| Variance (s) | Float (≥0) | `0.5` | Rango de variación aleatoria (solo si Randomize = True) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Resultados

| Condición | `_bt_last_result` |
|-----------|-------------------|
| Temporizador < Duration | `'RUNNING'` |
| Temporizador ≥ Duration | `'SUCCESS'` (y reinicia el timer) |

## Comportamiento del timer

El timer se inicializa a `0.0` la primera vez (usando `hasattr` como guardia). Cada frame suma `deltaTime()`. Cuando alcanza la duración objetivo:

1. Reinicia a `0.0`.
2. Si `Randomize = True`, recalcula una nueva duración objetivo para el siguiente ciclo.
3. Devuelve `SUCCESS`.

Esto permite usar BTWait en **bucles**: conectado dentro de un Selector cíclico, esperará N segundos, devolverá `SUCCESS`, y el siguiente frame el ciclo lo reinicia automáticamente.

## Uso típico

### Pausa antes de retomar patrulla

```
[BT Condition: reached_waypoint] 
    ├── True ──► [BT Wait: 2.0s] → [BT Selector]
    │                                    ├── Success ──► [BT Set Blackboard: next_waypoint]
    │                                    └── Failure ──► (continúa esperando)
    └── False ──► [BT Move To: waypoint]
```

### Delay aleatorio entre acciones (comportamiento orgánico)

```
[BT Custom Task: pick_action] → [BT Wait: Duration=0.5, Randomize=True, Variance=1.5]
```

El agente espera entre 0.5 y 2.0 segundos antes de evaluar la siguiente acción.

### Timeout de acción (junto con BTRepeater)

```
[BT Move To: target] → [BT Repeater: N=60] → [BT Selector]
                  ^           (60 SUCCESS = ~60 frames en ruta)
```

O directamente con Wait para forzar un cooldown después de fallar:

```
[BT Sequence]
    ├── Continue ──► [BT Custom Task: attack] → [BT Sequence]
    │                   (si falla el ataque)        └── Abort ──► [BT Wait: 1.0s]
    └── Abort ──► ...
```

## Notas

- El estado del timer (`_btw_<sid>`) usa el nombre del nodo en el editor para generar un identificador único. Si renombras el nodo, el estado se reinicia.
- Duration `0.0` devuelve `SUCCESS` inmediatamente (el timer arranca en 0, la primera comparación ya lo cumple).
