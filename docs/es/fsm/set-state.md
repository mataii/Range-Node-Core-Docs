# Set State

**Tipo:** Exec (In → Out)  
**Categoría:** FSM

Transiciona la máquina de estados a un nuevo estado. Reinicia el temporizador de estado. Es un **no-op** si el objeto ya está en el estado destino, lo que evita reiniciar el temporizador en cada frame.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM. Múltiples FSMs en el mismo objeto usan IDs distintos |
| New State | String | `"idle"` | Estado destino (solo si el socket State no está conectado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| State | Entrada | Data (String — estado destino dinámico) |
| Out | Salida | Exec |

## Código generado

```python
_fsm_new = str("idle")
if self.own.get('fsm_fsm_state', '') != _fsm_new:
    self.own['fsm_fsm_state'] = _fsm_new
    self._fsm_fsm_t = Range.logic.getClockTime()
```

Si el socket State está conectado, `"idle"` se reemplaza por la expresión del socket.

## Comportamiento

1. Convierte el valor destino a `str()` (acepta cualquier tipo).
2. Compara con el estado actual en `self.own['fsm_<id>_state']`.
3. Si son distintos: escribe el nuevo estado en la propiedad BGE y registra el tiempo actual.
4. Si son iguales: no hace nada (no-op).

## Uso típico

### Transición simple

```
[On Key Press: E, activated]
    └── True ──► [Set State: FSM ID="door", New State="opening"]
```

### Transición desde datos (socket State)

```
[Get State: FSM ID="door"]  ──(State)──► [Set State: FSM ID="door"]
```

Útil para copiar el estado de un objeto a otro.

### Transición dinámica desde blackboard

```
[BT Set Blackboard: next_state = "attack"]
...
[Set State: FSM ID="enemy", State = self._bt_bb.get('next_state', 'idle')]
```

### Encadenado con lógica de condición

```
[On Update] → [On State: FSM ID="fsm", State="patrol"]
                  └── True ──► [Distance Check: target]
                                    └── Near ──► [Set State: FSM ID="fsm", New State="chase"]
```

### Múltiples FSMs en el mismo objeto

```
[Set State: FSM ID="move",   New State="running"]
[Set State: FSM ID="anim",   New State="run_anim"]
[Set State: FSM ID="combat", New State="armed"]
```

Cada `Set State` escribe en una propiedad BGE independiente.

## Notas

- El estado se escribe en `self.own['fsm_<id>_state']` — la propiedad BGE debe existir previamente **o** Range la crea automáticamente al asignarla.
- El FSM ID se sanitiza: `"my door"` → `"my_door"`, produciendo `self.own['fsm_my_door_state']`.
- El temporizador `self._fsm_<id>_t` usa `getClockTime()` (reloj de pared absoluto), no `deltaTime`.
- Conectar el socket State anula la propiedad New State del panel — si ambos están configurados, el socket tiene prioridad.
