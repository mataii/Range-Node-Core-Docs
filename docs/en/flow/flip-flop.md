# Flip Flop

**Type:** Branch (True / False)  
**Category:** FLOW

Alternates between the `True` and `False` outputs on each call. The first call executes `True`, the second `False`, the third `True`, and so on. Useful for toggle mechanics.

## Properties

None — the node only maintains a state flag.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (odd calls: 1st, 3rd, 5th…) |
| False | Output | Exec (even calls: 2nd, 4th, 6th…) |

## Behavior

```python
if not hasattr(self, '_ff_<name>'):
    self._ff_<name> = False
self._ff_<name> = not self._ff_<name>
if self._ff_<name>:
    # → True path
else:
    # → False path
```

The flag starts at `False` and is **negated before the check**, so:

| Call | State before | Negated | Path |
|------|-------------|---------|------|
| 1st | False | **True** | True |
| 2nd | True | **False** | False |
| 3rd | False | **True** | True |
| … | … | … | … |

## Typical usage

### Door open/close

```
[OnCollision: button] → [Flip Flop]
                            ├── True  ──► [BTCustomTask: open_door()]
                            └── False ──► [BTCustomTask: close_door()]
```

### Alternating attack animations (left/right)

```
[BT Condition: attack_requested]
    └── True ──► [Flip Flop]
                    ├── True  ──► [BT Play Animation: "AttackLeft"]
                    └── False ──► [BT Play Animation: "AttackRight"]
```

### Blinking light (with Delay)

```
[OnUpdate] → [Delay: 0.5s, Repeat=True]
                 └── True ──► [Flip Flop]
                                 ├── True  ──► [BTCustomTask: light.energy = 10]
                                 └── False ──► [BTCustomTask: light.energy = 0]
```

### Stealth mode toggle

```
[OnKeyboard: Tab] → [Flip Flop]
                        ├── True  ──► [BT Set Blackboard: stealth = True]
                        └── False ──► [BT Set Blackboard: stealth = False]
```

## Notes

- The state persists while the object exists. If the object is destroyed and recreated, Flip Flop starts from `True` again.
- The node name determines the internal variable — give unique names if there are multiple Flip Flops in the same component.
- Unlike Do Once, Flip Flop **never ends** — it always alternates indefinitely.
- To reset to initial state: `self._ff_NodeName = False`.
