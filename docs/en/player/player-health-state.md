# Player Health State

**Type:** Data (no exec sockets)  
**Category:** PLAYER

Pure data node. Reads the health state stored by **Player Health** on the same object.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| Data | Enum | Value to expose |

### Data selector options

| Option | Type | Description |
|--------|------|-------------|
| Current HP | float | Current HP (`_ph_hp`) |
| Max HP | float | Maximum HP (`_ph_max_hp`) |
| Is Alive | bool | `True` if HP > 0 |
| Percent | float (0.0–1.0) | Current HP / Max HP |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data |

## Typical usage

```
[Player Health State: PERCENT] ──► [UI Health Bar: Set Value]
[Player Health State: CURRENT_HP] ──► [Compare: < 20] ──► [Play Low HP Sound]
```

!!! info "Requires Player Health on the same object"
    Without Player Health, returns `100.0` / `True` / `1.0`.
