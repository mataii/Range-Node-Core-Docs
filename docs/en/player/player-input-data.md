# Player Input Data

**Type:** Data (no exec sockets)  
**Category:** PLAYER

Pure data node. Reads a `_pi_*` variable published by **Player Input** on the same object and exposes it as a data socket to connect to other nodes.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| Data | Enum | Variable to read (see table below) |

### Data selector options

| Option | Variable read | Type | Default |
|--------|---------------|------|---------|
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

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data |

## Typical usage

Connect the `Value` socket to the input socket of any node that needs to react to player state without using exec logic:

```
[Player Input Data: FIRE] ──► [Compare] ──► [Branch] ──► [Play Sound]
[Player Input Data: MOVE_X] ──► [Math: Multiply] ──► [Set Property]
```

!!! info "Requires Player Input on the same object"
    If Player Input is not present, all values return their default (0.0 / False / 0).
