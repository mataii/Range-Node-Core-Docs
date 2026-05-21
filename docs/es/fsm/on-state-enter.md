# On State Enter

**Tipo:** Branch (On Enter / Otherwise)  
**Categoría:** FSM

Dispara el camino `On Enter` exactamente **un frame** cuando la FSM entra al estado dado. Ideal para lógica de inicialización: reproducir una animación, activar un sonido, generar un efecto de partículas — cualquier cosa que debe ocurrir una sola vez al entrar.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM a monitorizar |
| State | String | `"idle"` | Estado cuya entrada se detecta |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| On Enter | Salida | Exec (primer frame en el estado dado) |
| Otherwise | Salida | Exec (cualquier otro frame) |

## Código generado

```python
_fse_cur  = self.own.get('fsm_fsm_state', '')
_fse_prev = getattr(self, '_fsm_fsm_prev', None)
self._fsm_fsm_prev = _fse_cur
if _fse_cur == 'idle' and _fse_prev != 'idle':
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

La condición `cur == target and prev != target` garantiza que solo dispare en el frame exacto de la transición.

## Comportamiento frame a frame

```
Frame 1: estado = "patrol",  prev = None     → Otherwise
Frame 2: estado = "patrol",  prev = "patrol" → Otherwise
Frame 3: estado = "chase",   prev = "patrol" → Otherwise  (estado diferente)
Frame 4: estado = "idle",    prev = "chase"  → On Enter ✓  ← transición detectada
Frame 5: estado = "idle",    prev = "idle"   → Otherwise
Frame 6: estado = "idle",    prev = "idle"   → Otherwise
```

`On Enter` solo dispara en el frame 4 — aunque el objeto permanezca en "idle" indefinidamente.

## Uso típico

### Reproducir animación al entrar

```
[On Update] → [On State Enter: FSM ID="door", State="opening"]
                  └── On Enter ──► [BTCustomTask: self.own.playAction('door_open', 1, 30)]
```

### Sonido de entrada

```
[On Update] → [On State Enter: FSM ID="enemy", State="alert"]
                  └── On Enter ──► [BTCustomTask: play_sound('alert_voice')]
```

### Inicializar variables al entrar

```
[On Update] → [On State Enter: FSM ID="combat", State="attacking"]
                  └── On Enter ──► [BT Set Blackboard: attack_timer = 0.0]
                                        → [BT Set Blackboard: hits_landed = 0]
```

### Múltiples estados con lógica de entrada diferente

```
[On Update] → [On State Enter: FSM ID="pickup", State="collected"]
                  └── On Enter ──► [BTCustomTask: play_collect_sfx()]

[On Update] → [On State Enter: FSM ID="pickup", State="respawning"]
                  └── On Enter ──► [BTCustomTask: start_respawn_vfx()]
```

### Combinado con On State (continua + entrada)

```
# Lógica continua mientras está en el estado:
[On Update] → [On State: FSM ID="fsm", State="burning"]
                  └── True ──► [BTCustomTask: apply_damage_over_time()]

# Lógica de entrada (una sola vez):
[On Update] → [On State Enter: FSM ID="fsm", State="burning"]
                  └── On Enter ──► [BTCustomTask: play_ignite_sound()]
```

## Notas

- `_fsm_<id>_prev` se inicializa a `None` en el primer frame — por eso el primer frame en cualquier estado **sí** dispara `On Enter` (ya que `None != target`). Si el estado inicial está fijado por `SetState` en `On Start`, el primer `On State Enter` en ese estado se activará en el primer `On Update`.
- On State Enter y On State Exit usan atributos Python separados (`_fsm_<id>_prev` vs `_fsm_<id>_xprev`) para poder coexistir sin interferirse.
- El nodo no tiene socket State dinámico — el estado a detectar siempre se especifica como propiedad fija. Para detección dinámica, usa `On Property Change` sobre `self.own['fsm_<id>_state']`.
