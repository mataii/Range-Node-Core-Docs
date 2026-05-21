# Delay

**Tipo:** Branch (True / False)  
**Categoría:** FLOW

Ejecuta el camino `True` después de que han transcurrido N segundos desde la primera vez que se activó. Mientras espera, ejecuta el camino `False`. Con `Repeat=True` se reactiva cada N segundos (heartbeat).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Delay (s) | Float (≥0.0) | `1.0` | Segundos de espera antes de disparar |
| Repeat | Bool | `False` | Si True, vuelve a disparar cada N segundos indefinidamente |

El socket de datos `Delay` puede anular la propiedad en runtime.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Delay | Entrada | Data (Float) |
| True | Salida | Exec (cuando el tiempo expira) |
| False | Salida | Exec (mientras espera) |

## Comportamiento

### Primer frame de ejecución

Inicializa el temporizador con la hora de disparo objetivo:

```python
self._dly_<nombre> = Range.logic.getClockTime() + delay
self._dly_<nombre>_done = False
```

### Frames siguientes

```python
if not self._dly_<nombre>_done and Range.logic.getClockTime() >= self._dly_<nombre>:
    if repeat:
        self._dly_<nombre> = Range.logic.getClockTime() + delay  # rearmarse
    else:
        self._dly_<nombre>_done = True  # disparar solo una vez
    # → True path
else:
    # → False path
```

### Implementación con `getClockTime()`

El Delay usa el **reloj absoluto** de Range (`getClockTime()`), no una acumulación de `deltaTime()`. Esto significa:

- Es preciso independientemente del framerate.
- El objetivo se fija en el momento en que se inicializa (primera vez que se ejecuta el nodo).
- **Debe estar conectado a OnUpdate** para comprobar el tiempo cada frame.

### Repeat = False (por defecto)

Dispara `True` exactamente una vez y luego entra en modo silencioso (`_done = True`). El camino `False` ya no se ejecuta tampoco — el nodo queda inactivo.

### Repeat = True

Cada vez que dispara, rearma el temporizador con `currentTime + delay`. Produce un heartbeat periódico mientras el nodo siga recibiendo ejecución.

## Uso típico

### Esperar 2 segundos antes de una acción

```
[OnUpdate] → [Delay: 2.0s, Repeat=False]
                 ├── True  ──► [BTCustomTask: trigger_cutscene()]
                 └── False ──► (esperando)
```

### Heartbeat cada 0.5 segundos (polling periódico)

```
[OnUpdate] → [Delay: 0.5s, Repeat=True]
                 ├── True  ──► [Enemy Perception]   # percepción cada medio segundo
                 └── False ──► (nada entre ticks)
```

### Delay dinámico desde socket

```python
# En un BT Set Blackboard o directamente en el socket Data:
Value: self._bt_bb.get('respawn_time', 3.0)
```

```
[Delay: Delay = self._bt_bb['respawn_time']]
```

### Combinado con Do Once para acción retardada única

```
[OnUpdate] → [Do Once]
                 └── True ──► [Delay: 5.0s, Repeat=False]
                                  └── True ──► [BTCustomTask: start_ambush()]
```

### Luz parpadeante con Flip Flop

```
[OnUpdate] → [Delay: 0.3s, Repeat=True]
                 └── True ──► [Flip Flop]
                                 ├── True  ──► [BTCustomTask: lamp.energy = 5.0]
                                 └── False ──► [BTCustomTask: lamp.energy = 0.0]
```

## Comparación con BT Wait

| Delay | BT Wait |
|-------|---------|
| Categoría FLOW | Categoría AI_BT |
| Conectado a OnUpdate directo | Conectado en árbol BT |
| False path = "mientras espero" | No tiene path de espera |
| Puede repetir con Repeat=True | Reinicia solo al completar |
| Usa `getClockTime()` | Acumula `deltaTime()` |

## Notas

- Si `Delay = 0.0`: el nodo puede disparar en el mismo frame que se inicializa (porque `getClockTime() >= getClockTime() + 0` es verdadero). Para evitar esto, usa un valor mínimo de `0.016`.
- Con `Repeat=False`: una vez que `_done = True`, el nodo **deja de ejecutar ambos caminos** — ni True ni False. El flujo de ejecución simplemente termina en ese nodo.
- Con `Repeat=True`: el temporizador se rearma desde el momento del disparo, no desde el inicio. Pequeñas variaciones de framerate pueden causar jitter en el intervalo — es una aproximación, no un timer de precisión.
- Para múltiples delays independientes, usar nodos Delay con nombres distintos.
