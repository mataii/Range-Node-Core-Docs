# Gate

**Tipo:** Branch (Out / Skip)  
**Categoría:** FLOW

Deja pasar la ejecución una vez por cada N llamadas. Las llamadas intermedias van a `Skip`. Útil para reducir la frecuencia de operaciones costosas o para ritmos de disparo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Every N calls | Int (≥1) | `2` | Número de llamadas entre cada disparo |

El socket de datos `Count` puede anular esta propiedad en runtime.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Count | Entrada | Data (Int) |
| Out | Salida | Exec (cada N llamadas) |
| Skip | Salida | Exec (las demás) |

## Comportamiento

```python
if not hasattr(self, '_gate_<nombre>'):
    self._gate_<nombre> = 0
self._gate_<nombre> += 1
if self._gate_<nombre> >= N:
    self._gate_<nombre> = 0
    # → Out path
else:
    # → Skip path
```

El contador empieza en 0 y se incrementa cada llamada. Cuando llega a N, dispara `Out` y se resetea a 0.

### Tabla de disparos con N=3

| Llamada | Contador antes | Acción |
|---------|----------------|--------|
| 1ª | 0→1 | Skip |
| 2ª | 1→2 | Skip |
| 3ª | 2→3 → reset a 0 | **Out** |
| 4ª | 0→1 | Skip |
| 5ª | 1→2 | Skip |
| 6ª | 2→3 → reset a 0 | **Out** |

El primer disparo ocurre en la llamada número N, no en la N+1.

## Uso típico

### Lógica costosa cada 3 frames de OnUpdate

```
[OnUpdate] → [Gate: N=3]
                 ├── Out  ──► [BTCustomTask: expensive_recalculation()]
                 └── Skip ──► (nada)
```

### Disparo cada 5 pasos en una patrulla

```
[Patrol] → [Gate: N=5]
                ├── Out  ──► [BTCustomTask: check_environment()]
                └── Skip ──► (continuar)
```

### Frecuencia dinámica desde socket

Conectar el socket `Count` a una variable del blackboard:

```
BT Set Blackboard: difficulty_n = 2    (fácil)
BT Set Blackboard: difficulty_n = 5    (difícil)
```

```
[Gate: Count = self._bt_bb.get('difficulty_n', 3)]
```

### Ritmo de disparo (ej. una bala cada 4 ticks de OnUpdate)

```
[OnUpdate] → [Gate: N=4]
                 ├── Out  ──► [BTCustomTask: spawn_bullet()]
                 └── Skip ──► (esperar)
```

## Notas

- El socket `Count` acepta expresiones Python. Si no está conectado, usa la propiedad `Every N calls`.
- El primer Out ocurre en la llamada #N, no en la #1. Si necesitas que dispare en la primera llamada, usa N=1.
- Con N=1: Out en todas las llamadas (el Gate no bloquea nada — comportamiento equivalente a un nodo sin gate).
- El contador persiste mientras el objeto exista. No se resetea entre escenas si el objeto persiste.
- Para resetear el contador manualmente: `self._gate_NombreDelNodo = 0`.
