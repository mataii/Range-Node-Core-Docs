# Get State

**Tipo:** Data  
**Categoría:** FSM

Nodo de datos que devuelve el nombre del estado actual de la FSM como cadena de texto. Se usa como fuente de datos en sockets de entrada de otros nodos.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM a consultar |
| Default | String | `"idle"` | Valor devuelto antes de que se llame a `SetState` por primera vez |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| State | Salida | Data (String) |

## Código generado

```python
self.own.get('fsm_fsm_state', 'idle')
```

Es una expresión Python pura — se inserta directamente donde se conecte el socket.

## Uso típico

### Mostrar estado en propiedad BGE (debugging)

```
[On Update] → [BTCustomTask:
                   self.own['debug_state'] = {Get State: FSM ID="fsm"}]
```

### Pasar estado a otro nodo Set State

```
[Get State: FSM ID="source_fsm"] ──(State)──► [Set State: FSM ID="target_fsm"]
```

Copia el estado de una FSM a otra (útil para sincronizar objetos).

### Condición sobre el estado actual

```
[On Update] → [BT Condition: {Get State: FSM ID="door"} == "open"]
                  └── True ──► [allow_passage()]
```

### Loguear transiciones

```
[On Update] → [On Property Change: Property="fsm_door_state"]
                  └── Changed ──► [BTCustomTask:
                                       print(f"Door: {_pc_old} → {_pc_new}")]
```

(Aquí se usa `On Property Change` directamente sobre la propiedad BGE — `Get State` no es necesario para esto.)

### Almacenar estado en blackboard

```
[On Update] → [BT Set Blackboard: last_state = {Get State: FSM ID="ai"}]
```

## Notas

- `Get State` lee `self.own.get('fsm_<id>_state', default)` — si la propiedad no existe aún (antes de la primera llamada a `SetState`), devuelve el valor `Default` configurado.
- Como nodo Data puro, no tiene socket In ni Out Exec — solo se conecta a sockets de entrada de tipo Data en otros nodos.
- El valor devuelto siempre es `str` (las propiedades BGE de tipo string).
- Para comparar con una lista de posibles estados, usa una expresión en `BT Condition`: `{Get State} in ['idle', 'patrol']`.
