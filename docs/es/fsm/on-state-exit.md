# On State Exit

**Tipo:** Branch (On Exit / Otherwise)  
**Categoría:** FSM

Dispara el camino `On Exit` exactamente **un frame** cuando la FSM abandona el estado dado. Ideal para lógica de limpieza: desactivar efectos, detener sonidos en bucle, restaurar variables — cualquier cosa que debe ocurrir una sola vez al salir.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM a monitorizar |
| State | String | `"idle"` | Estado cuya salida se detecta |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| On Exit | Salida | Exec (primer frame tras abandonar el estado) |
| Otherwise | Salida | Exec (cualquier otro frame) |

## Código generado

```python
_fsx_cur  = self.own.get('fsm_fsm_state', '')
_fsx_prev = getattr(self, '_fsm_fsm_xprev', None)
self._fsm_fsm_xprev = _fsx_cur
if _fsx_prev == 'idle' and _fsx_cur != 'idle':
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

La condición `prev == target and cur != target` detecta el frame exacto en que el objeto deja el estado.

## Comportamiento frame a frame

```
Frame 1: estado = "patrol",  xprev = None     → Otherwise
Frame 2: estado = "idle",    xprev = "patrol" → Otherwise  (estado anterior ≠ "idle")
Frame 3: estado = "idle",    xprev = "idle"   → Otherwise
Frame 4: estado = "chase",   xprev = "idle"   → On Exit ✓  ← salida detectada
Frame 5: estado = "chase",   xprev = "chase"  → Otherwise
Frame 6: estado = "attack",  xprev = "chase"  → Otherwise  (estado anterior ≠ "idle")
```

`On Exit` solo dispara en el frame 4 — el primer frame en que el estado ya no es "idle".

## Uso típico

### Detener sonido en bucle al salir

```
[On Update] → [On State Exit: FSM ID="vehicle", State="engine_on"]
                  └── On Exit ──► [BTCustomTask: stop_loop_sound('engine')]
```

### Desactivar efecto visual al salir

```
[On Update] → [On State Exit: FSM ID="player", State="invisible"]
                  └── On Exit ──► [BTCustomTask:
                                       self.own.color = [1, 1, 1, 1]
                                       self.own['invisible'] = False]
```

### Guardar estado antes de salir

```
[On Update] → [On State Exit: FSM ID="combat", State="attacking"]
                  └── On Exit ──► [BTCustomTask:
                                       self._bt_bb['last_attack_time'] = Range.logic.getClockTime()]
```

### Combinado con On State Enter (ciclo completo)

```
# Al entrar a "charging":
[On Update] → [On State Enter: FSM ID="weapon", State="charging"]
                  └── On Enter ──► [BTCustomTask: start_charge_vfx()]

# Al salir de "charging":
[On Update] → [On State Exit: FSM ID="weapon", State="charging"]
                  └── On Exit ──► [BTCustomTask: stop_charge_vfx()]
```

### Transición con limpieza

```
# En cualquier transición desde "chase":
[On Update] → [On State Exit: FSM ID="ai", State="chase"]
                  └── On Exit ──► [BTCustomTask:
                                       self.ai_move_target = None
                                       self.ai_target_pos  = None]
```

## Diferencia con On State Enter

| On State Enter | On State Exit |
|---------------|---------------|
| Dispara al **llegar** al estado | Dispara al **abandonar** el estado |
| `cur == target and prev != target` | `prev == target and cur != target` |
| Atributo: `_fsm_<id>_prev` | Atributo: `_fsm_<id>_xprev` |

Ambos nodos pueden coexistir en el mismo árbol monitorizando el mismo estado — usan atributos distintos y no se interfieren.

## Notas

- En el primer frame, `_fsx_prev` es `None` — el nodo no dispara `On Exit` al inicio aunque el estado inicial sea distinto del objetivo (porque `None != target` en la condición `prev == target`).
- Si `SetState` cambia el estado en el mismo frame en que se llama a `On State Exit`, el nodo detectará el cambio en el siguiente frame.
- Para detectar la salida de **cualquier** estado (no uno concreto), usa [On Property Change](../event/on-property-change.md) sobre `self.own['fsm_<id>_state']`.
