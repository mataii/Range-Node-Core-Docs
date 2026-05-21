# Player Input

**Type:** Exec (In → Out)  
**Category:** PLAYER

Reads the keyboard and mouse every frame and stores the normalized state in `_pi_*` variables on the object. It is the first node in the player pipeline — connect it directly to `OnUpdate`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Forward | Enum | `W` | Move forward key |
| Backward | Enum | `S` | Move backward key |
| Left | Enum | `A` | Move left key |
| Right | Enum | `D` | Move right key |
| Jump | Enum | `Space` | Jump key |
| Reload | Enum | `R` | Reload key |
| Sprint | Enum | `Left Shift` | Sprint key (hold) |
| Fire: Left Mouse Button | Bool | `True` | Use left mouse button as fire |
| Slot: Scroll Wheel | Bool | `True` | Use mouse wheel to change inventory slot |
| Slot Count | Int (1–10) | `10` | Number of active inventory slots |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published state (`_pi_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `_pi_move_x` | `float` | Lateral axis: −1 (left), 0, +1 (right) |
| `_pi_move_y` | `float` | Forward axis: −1 (back), 0, +1 (forward) |
| `_pi_is_moving` | `bool` | `True` while any movement key is active |
| `_pi_sprint` | `bool` | `True` while Sprint key is held (`.active`) |
| `_pi_jump` | `bool` | `True` only on the frame Jump was pressed (`.activated`) |
| `_pi_fire` | `bool` | `True` only on the frame Fire was pressed (`.activated`) |
| `_pi_reload` | `bool` | `True` only on the frame Reload was pressed (`.activated`) |
| `_pi_slot_changed` | `bool` | `True` only on the frame the active slot changed |
| `_pi_slot` | `int` | Current active slot index (0–9) |

!!! note "`.activated` vs `.active`"
    - `.activated` → `True` for **one frame only** (rising edge, equivalent to "just pressed").
    - `.active` → `True` **while held down** (continuous).
    
    `_pi_sprint` uses `.active`; everything else uses `.activated`.

## Typical usage

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → ...
```

All nodes that need to know whether the player is moving, jumping, firing, etc. must be on the **same object** as this node and read their corresponding `_pi_*` variables.

## Notes

- Inventory slot state (0–9) can also be changed with the mouse wheel when `Slot: Scroll Wheel` is enabled.
- `_pi_fire` is also exposed as `self.input_fire` for compatibility with Weapon Fire Executor.
