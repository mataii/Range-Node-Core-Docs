# On Timer

**Tipo:** Branch (Elapsed / Waiting)  
**Categoría:** EVENT

Temporizador de cuenta atrás propio. Cuenta hacia atrás desde `Duration` segundos y dispara `Elapsed` al llegar a cero. Con `Repeat=True` se reinicia automáticamente. Expone el progreso (0→1) como socket de dato.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Duration (s) | Float (≥0.01) | `1.0` | Duración de la cuenta atrás en segundos |
| Repeat | Bool | `True` | Reiniciar automáticamente al disparar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Elapsed | Salida | Exec (cuando el timer llega a 0) |
| Waiting | Salida | Exec (mientras cuenta) |
| Progress | Salida | Data (Float 0→1) |

## Comportamiento

```python
if not hasattr(self, '_tmr_<nombre>'):
    self._tmr_<nombre> = duration

if self._tmr_<nombre> >= 0:
    self._tmr_<nombre> -= deltaTime()

progress = max(0.0, 1.0 - timer / duration)

if timer <= 0:
    self._tmr_<nombre> = duration  # Repeat=True
    # (o -1.0 si Repeat=False)
    # → Elapsed
else:
    # → Waiting
```

### Socket Progress

`progress = 1.0 - timer / duration`:
- Al inicio (timer = duration): `progress = 0.0`
- A mitad del tiempo: `progress = 0.5`
- Al disparar (timer = 0): `progress = 1.0`

Útil para barras de carga, fade, transiciones suaves.

### Repeat = True (por defecto)

Al disparar, el timer se reinicia a `duration`. Produce un heartbeat periódico.

### Repeat = False

Al disparar, el timer se pone a `-1.0`. El nodo deja de ejecutar ambos caminos (queda permanentemente inactivo).

## Diferencia con Delay (FLOW)

| On Timer | Delay |
|----------|-------|
| Categoría EVENT | Categoría FLOW |
| Punto de entrada propio (sin raíz) | Requiere OnUpdate como raíz |
| Usa `deltaTime()` | Usa `getClockTime()` |
| Expone Progress (0→1) | Sin progreso |
| Repeat = True por defecto | Repeat = False por defecto |
| Timer interno siempre contando | Timer se inicializa al primer frame |

> Usar **On Timer** cuando la cadena de nodos no tiene otra raíz. Usar **Delay** dentro de cadenas que ya arrancan de OnUpdate.

## Uso típico

### Heartbeat de IA cada 0.5 segundos

```
[On Timer: 0.5s, Repeat=True] → [Enemy Perception]
                                       → [AI State Machine]
```

### Spawn periódico de enemigos

```
[On Timer: 10.0s, Repeat=True]
    └── Elapsed ──► [BTCustomTask:
                       self.own.scene.addObject('Enemy', self.own, 0)]
```

### Barra de carga visual

```
[On Timer: 3.0s, Repeat=False]
    └── Waiting ──► [BTCustomTask:
                       ui_bar.localScale.x = _tmr_progress]
    └── Elapsed ──► [BTCustomTask: complete_loading()]
```

### Cuenta atrás de ronda

```
[On Timer: 60.0s, Repeat=False]
    ├── Waiting ──► [BTCustomTask:
                       self.own['time_left'] = int((1.0 - _tmr_progress) * 60)]
    └── Elapsed ──► [BTCustomTask: end_round()]
```

## Notas

- El timer arranca automáticamente al crearse el nodo (primer frame). No espera a que el objeto sea activado.
- El nombre del nodo determina la variable interna `_tmr_<nombre>`. Renombrar el nodo resetea el timer.
- Con `Repeat=False`, una vez agotado el timer, el nodo **deja de ejecutar** ambos caminos — la cadena completa se detiene en ese punto.
- La variable `_tmr_progress` está disponible en el código local de los nodos siguientes (incluyendo BTCustomTask).
- Para suspender el timer, usar un FLOW Gate o Do Once que bloquee la entrada.
