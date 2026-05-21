# Categoría FSM

La categoría **FSM** (Finite State Machine — Máquina de Estados Finitos) proporciona un sistema genérico y ligero para gestionar estados en cualquier tipo de objeto: puertas, palancas, NPCs, pickups, elementos de UI, etc.

## Patrón de uso

```
1. SetState   → cambia el estado desde cualquier cadena de eventos
2. OnState    → ramifica la lógica por estado desde un OnUpdate
3. OnStateEnter → lógica de entrada de un frame (animación, sonido, VFX)
4. OnStateExit  → lógica de salida de un frame (limpieza, transición)
5. GetState   → fuente de datos: nombre del estado actual
6. GetStateTime → fuente de datos: segundos en el estado actual
```

## Nodos

| Nodo | Tipo | Función |
|------|------|---------|
| [Set State](set-state.md) | Exec (In → Out) | Transiciona a un nuevo estado |
| [On State](on-state.md) | Branch (True/False) | Verdadero mientras se esté en el estado dado |
| [On State Enter](on-state-enter.md) | Branch (On Enter/Otherwise) | Dispara un frame al entrar al estado |
| [On State Exit](on-state-exit.md) | Branch (On Exit/Otherwise) | Dispara un frame al salir del estado |
| [Get State](get-state.md) | Data | Devuelve el nombre del estado actual |
| [Get State Time](get-state-time.md) | Data | Devuelve segundos transcurridos en el estado actual |

---

## Cómo funciona internamente

El estado se almacena como **propiedad BGE** en el objeto:

```
self.own['fsm_<id>_state'] = "idle"
```

Esto permite que el nodo [On Property Change](../event/on-property-change.md) reaccione a cambios de estado sin ninguna configuración extra.

El temporizador de estado se registra como atributo Python:

```python
self._fsm_<id>_t = Range.logic.getClockTime()
```

`GetStateTime` devuelve `getClockTime() - self._fsm_<id>_t`.

---

## Múltiples FSMs en el mismo objeto

Cada FSM se identifica por un **FSM ID** (cadena de texto). Si un mismo objeto necesita gestionar varios estados independientes, se usan IDs distintos:

```
FSM ID = "move"     → self.own['fsm_move_state']
FSM ID = "anim"     → self.own['fsm_anim_state']
FSM ID = "dialogue" → self.own['fsm_dialogue_state']
```

Todos los nodos que pertenecen al mismo FSM deben compartir el mismo FSM ID.

---

## Ejemplo completo: puerta

```
# Transición a "opening" al pulsar E:
[On Update] → [On Key Press: E, activated]
                  └── True ──► [Set State: FSM ID="door", State="opening"]

# Lógica de estado:
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [BTCustomTask: play_anim('open')]

[On Update] → [On State Enter: FSM ID="door", State="opening"]
                  └── On Enter ──► [BTCustomTask: play_sound('door_creak')]

[On Update] → [On State Enter: FSM ID="door", State="open"]
                  └── On Enter ──► [BTCustomTask: self.own['collidable'] = False]

# Después de 2 segundos en "opening", pasar a "open":
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [BTCustomTask:
                                    if Range.logic.getClockTime() - _door_t > 2.0:
                                        pass]  # usar GetStateTime en su lugar →

[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Get State Time: FSM ID="door"]
                                    → [BT Condition: Seconds >= 2.0]
                                          └── True ──► [Set State: FSM ID="door", State="open"]
```

---

## Diferencia con AI State Machine

| FSM (esta categoría) | AI State Machine (categoría AI) |
|---------------------|---------------------------------|
| Propósito general — cualquier objeto | Específico para IA de enemigos |
| FSM ID personalizable | Variables `ai_state`, `ai_prev_state` fijas |
| Sin `execution_order` | `execution_order = 50` |
| Sin integración con pipeline AI | Integrado con EnemyPerception, EnemyCombat, etc. |

Para enemigos con pipeline modular completo, usa [AI State Machine](../ai/ai-state-machine.md). Para cualquier otro objeto, usa esta categoría FSM.

---

## Notas

- `SetState` es un **no-op** si el objeto ya está en el estado destino — el temporizador no se reinicia.
- El FSM ID se sanitiza a identificador Python válido (`_safe`): espacios y guiones se convierten en `_`.
- El estado es una cadena de texto (`str`) — cualquier valor pasado por el socket Data se convierte con `str()`.
- Si nunca se llamó a `SetState`, `GetState` devuelve el valor `Default` configurado en el nodo.
- `GetStateTime` devuelve `0.0` antes de la primera llamada a `SetState`.
