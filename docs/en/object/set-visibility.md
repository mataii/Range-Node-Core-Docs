# Set Visibility

**Type:** Action  
**Category:** OBJECT

Shows or hides the object running the component using `setVisible()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Visible | Bool | `True` | `True` = show, `False` = hide |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Visible | Input | Data (Bool) |

## Generated code

```python
self.own.setVisible(True)
```

## Typical usage

### Hide object on death

```
[On Message: Subject="enemy_dead"] → [Set Visibility: Visible=False]
```

### Show/hide based on FSM state

```
[On Update] → [On State: FSM ID="player", State="invisible"]
                  ├── True ──► [Set Visibility: Visible=False]
                  └── Otherwise ──► [Set Visibility: Visible=True]
```

## Notes

- Hiding with `setVisible(False)` excludes from rendering but does **not** exclude from physics or collisions.
- To also exclude from collisions, use `suspendPhysics()` (available in the PHYSICS category).
- Operates on `self.own` — the object holding the component. To hide another object, use `BTCustomTask` with `scene.objects['name'].setVisible(bool)`.
