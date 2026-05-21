# On Property Change

**Type:** Branch (Changed / Same)  
**Category:** EVENT

Monitors a BGE object property and branches based on whether its value changed since the previous frame. Exposes both the new and old values.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"hp"` | Name of the BGE property to monitor on `self.own` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Changed | Output | Exec (value changed this frame) |
| Same | Output | Exec (value unchanged) |
| New Value | Output | Data (any — current value of the property) |
| Old Value | Output | Data (any — value from the previous frame) |

## Generated code

```python
_pc_new = self.own.get('hp', None)
_pc_old = getattr(self, '_prev_hp', _pc_new)
self._prev_hp = _pc_new

if _pc_new != _pc_old:
    # Changed path
else:
    # Same path
```

`_prev_<prop>` is initialized to the current value on the first frame, so the node does not fire Changed on startup.

## Typical usage

### React to HP change

```
[On Update] → [On Property Change: Property="hp"]
                  └── Changed ──► [BTCustomTask:
                                       if _pc_new < _pc_old:
                                           play_hurt_sound()
                                       else:
                                           play_heal_sound()]
```

### Detect state change

```
[On Update] → [On Property Change: Property="ai_state"]
                  └── Changed ──► [BTCustomTask:
                                       print(f"State: {_pc_old} → {_pc_new}")]
```

### Sync HUD on property change

```
[On Update] → [On Property Change: Property="score"]
                  └── Changed ──► [BTCustomTask:
                                       hud_obj['score_display'] = _pc_new]
```

### Threshold detection

```
[On Update] → [On Property Change: Property="hp"]
                  └── Changed ──► [BT Condition: _pc_new <= 25]
                                       └── True ──► [BTCustomTask: activate_low_hp_warning()]
```

## Notes

- The property must exist on `self.own` — if it does not exist, `self.own.get('hp', None)` returns `None` and the node treats it as the current value.
- Comparison uses Python's `!=` operator — works for int, float, string, and bool properties. Lists and dicts are compared by identity, not by content.
- `_pc_new` and `_pc_old` are local variables available in nodes connected to both the Changed and Same paths.
- To monitor a Python attribute (not a BGE property), use a [BTCustomTask](../ai/bt-custom-task.md) with manual `getattr` / `setattr` comparison instead.
- Multiple On Property Change nodes monitoring different properties each maintain their own `_prev_<prop>` variable.
