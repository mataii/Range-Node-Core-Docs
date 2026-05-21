# Player Input

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Lee el teclado y el mouse cada frame y almacena el estado normalizado en variables `_pi_*` del objeto. Es el primer nodo del pipeline de jugador — debe conectarse directamente a `OnUpdate`.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Forward | Enum | `W` | Tecla para avanzar |
| Backward | Enum | `S` | Tecla para retroceder |
| Left | Enum | `A` | Tecla para ir a la izquierda |
| Right | Enum | `D` | Tecla para ir a la derecha |
| Jump | Enum | `Space` | Tecla para saltar |
| Reload | Enum | `R` | Tecla para recargar |
| Sprint | Enum | `Left Shift` | Tecla para sprint (mantener) |
| Fire: Left Mouse Button | Bool | `True` | Usa el botón izquierdo del mouse como disparo |
| Slot: Scroll Wheel | Bool | `True` | Usa la rueda del mouse para cambiar de slot |
| Slot Count | Int (1–10) | `10` | Número de slots de inventario activos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Estado publicado (`_pi_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_pi_move_x` | `float` | Eje lateral: −1 (izquierda), 0, +1 (derecha) |
| `_pi_move_y` | `float` | Eje frontal: −1 (atrás), 0, +1 (adelante) |
| `_pi_is_moving` | `bool` | `True` mientras haya alguna tecla de movimiento activa |
| `_pi_sprint` | `bool` | `True` mientras la tecla Sprint esté presionada (`.active`) |
| `_pi_jump` | `bool` | `True` solo el frame en que se presionó Jump (`.activated`) |
| `_pi_fire` | `bool` | `True` solo el frame en que se presionó Fire (`.activated`) |
| `_pi_reload` | `bool` | `True` solo el frame en que se presionó Reload (`.activated`) |
| `_pi_slot_changed` | `bool` | `True` solo el frame en que el slot activo cambió |
| `_pi_slot` | `int` | Índice del slot activo actual (0–9) |

!!! note "Distinción `.activated` vs `.active`"
    - `.activated` → `True` **un solo frame** (flanco de subida, equivale a "acaba de presionarse").
    - `.active` → `True` **mientras esté presionada** (mantener).
    
    `_pi_sprint` usa `.active`; el resto usa `.activated`.

## Uso típico

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → ...
```

Todos los nodos que necesitan saber si el jugador se mueve, salta, dispara, etc., deben estar en el **mismo objeto** que este nodo y leer sus `_pi_*` correspondientes.

## Notas

- El estado de los slots de inventario (0–9) también puede cambiarse con la rueda del mouse si `Slot: Scroll Wheel` está activo.
- `_pi_fire` se expone también como `self.input_fire` para compatibilidad con Weapon Fire Executor.
