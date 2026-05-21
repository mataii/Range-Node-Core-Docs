# Player Input Data

**Tipo:** Data (sin sockets exec)  
**Categoría:** PLAYER

Nodo de datos puro. Lee una variable `_pi_*` publicada por **Player Input** en el mismo objeto y la expone como un socket de datos para conectar a otros nodos.

## Propiedades

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| Data | Enum | Variable a leer (ver tabla abajo) |

### Opciones del selector Data

| Opción | Variable leída | Tipo | Default |
|--------|----------------|------|---------|
| Move X | `_pi_move_x` | float | 0.0 |
| Move Y | `_pi_move_y` | float | 0.0 |
| Is Moving | `_pi_is_moving` | bool | False |
| Sprint | `_pi_sprint` | bool | False |
| Jump | `_pi_jump` | bool | False |
| Fire | `_pi_fire` | bool | False |
| Reload | `_pi_reload` | bool | False |
| Slot Changed | `_pi_slot_changed` | bool | False |
| Active Slot | `_pi_slot` | int | 0 |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Data |

## Uso típico

Conecta el socket `Value` al socket de entrada de cualquier nodo que necesite reaccionar al estado del jugador sin usar la lógica de exec:

```
[Player Input Data: FIRE] ──► [Compare] ──► [Branch] ──► [Play Sound]
[Player Input Data: MOVE_X] ──► [Math: Multiply] ──► [Set Property]
```

!!! info "Requiere Player Input en el mismo objeto"
    Si Player Input no está presente, todos los valores devuelven su default (0.0 / False / 0).
