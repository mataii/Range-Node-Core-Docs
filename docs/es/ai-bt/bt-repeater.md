# BT Repeater

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT

Decorador que acumula resultados `SUCCESS` del nodo anterior. Devuelve `RUNNING` hasta acumular N éxitos; al alcanzarlos devuelve `SUCCESS` y reinicia el contador.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Repeat N | Int (≥1) | `3` | Número de `SUCCESS` a acumular antes de propagar `SUCCESS` |
| Reset on Failure | Bool | `True` | Reinicia el contador si el nodo hijo devuelve `FAILURE` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Lógica por frame

```
frame
  │
  ├── _bt_last_result == 'SUCCESS':
  │       contador += 1
  │       Si contador >= N:
  │           contador = 0
  │           _bt_last_result = 'SUCCESS'   ← propaga éxito
  │       Si no:
  │           _bt_last_result = 'RUNNING'   ← sigue acumulando
  │
  ├── _bt_last_result == 'FAILURE' (si Reset on Failure = True):
  │       contador = 0
  │       _bt_last_result = 'FAILURE'
  │
  └── _bt_last_result == 'RUNNING':
          sin cambio
```

## Estado interno

El contador usa el nombre del nodo para generar una variable única: `self._btrep_<sid>`. Esto permite tener múltiples BTRepeater en el mismo objeto con contadores independientes.

## Uso típico

### Confirmar detección antes de reaccionar

```
[BT Condition: enemy_in_sight] → [BT Repeater: N=5] → [BT Selector]
                                                              ├── Success ──► [Alertar y atacar]
                                                              └── Failure ──► (sin reacción)
```

El agente solo se alerta si ve al enemigo durante 5 frames consecutivos (evita reacciones a visiones fugaces).

### Atacar N veces antes de retroceder

```
[BT Custom Task: attack] → [BT Repeater: N=3, Reset on Failure=False] → [BT Sequence]
                                                                               ├── Continue ──► [Retreat]
                                                                               └── Abort ──► (continúa atacando)
```

## Notas

- El contador persiste entre frames — es estado acumulativo, no por frame.
- Si `Reset on Failure = False`, los fallos no reinician el contador (el FAILURE se propaga pero el conteo queda donde estaba).
- `RUNNING` del hijo no modifica el contador — el Repeater espera a que el hijo resuelva `SUCCESS` o `FAILURE`.
