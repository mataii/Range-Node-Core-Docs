# On State

**Tipo:** Branch (True / False)  
**Categoría:** FSM

Comprueba si la máquina de estados está en un estado concreto y ramifica la ejecución. Se usa desde cadenas de [On Update](../event/on-update.md) para ejecutar lógica continua mientras el objeto permanece en ese estado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM a consultar |
| State | String | `"idle"` | Estado a comprobar (solo si el socket State no está conectado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| State | Entrada | Data (String — estado a comparar dinámicamente) |
| True | Salida | Exec (en el estado dado) |
| False | Salida | Exec (en otro estado) |

## Código generado

```python
if self.own.get('fsm_fsm_state', '') == str("idle"):
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Lógica continua por estado

```
[On Update] → [On State: FSM ID="door", State="open"]
                  └── True ──► [BTCustomTask: allow_player_through()]

[On Update] → [On State: FSM ID="door", State="closed"]
                  └── True ──► [BTCustomTask: block_player()]
```

### Varios estados para la misma lógica (encadenado)

On State no soporta múltiples estados en un único nodo — encadena dos nodos con el socket False:

```
[On Update] → [On State: State="walking"]
                  ├── True  ──► [apply_movement_anim()]
                  └── False ──► [On State: State="running"]
                                    └── True ──► [apply_run_anim()]
```

### Con timer de estado

```
[On Update] → [On State: FSM ID="charge", State="charging"]
                  └── True ──► [Get State Time: FSM ID="charge"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 3.0]
                                                       └── True ──► [Set State: State="fire"]
```

### Estado dinámico (socket State)

```
[BTCustomTask: _target_state = self._bt_bb.get('mode', 'idle')]
    → [On State: FSM ID="fsm", State=_target_state]
```

## Diferencia con On State Enter / Exit

| On State | On State Enter | On State Exit |
|----------|---------------|---------------|
| Verdadero **cada frame** en ese estado | Verdadero **solo el primer frame** al entrar | Verdadero **solo el primer frame** al salir |
| Para lógica continua | Para inicialización (sonidos, animaciones) | Para limpieza (desactivar efectos) |

## Notas

- Si nunca se llamó a `SetState`, `self.own.get('fsm_<id>_state', '')` devuelve `''` — ningún estado coincidirá hasta la primera transición.
- El socket State convierte el valor a `str()`, igual que `SetState`.
- Conectar el socket State anula la propiedad State del panel.
- Se pueden tener múltiples nodos `On State` para el mismo FSM ID — cada uno comprueba su propio estado de forma independiente en el mismo frame.
