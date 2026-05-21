# On State Exit

**Type:** Branch (On Exit / Otherwise)  
**Category:** FSM

Fires the `On Exit` path for exactly **one frame** when the FSM leaves the given state. Ideal for cleanup logic: stopping looping sounds, deactivating visual effects, restoring variables — anything that should happen once on exit.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | Identifier of the FSM to monitor |
| State | String | `"idle"` | State whose exit is detected |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| On Exit | Output | Exec (first frame after leaving the given state) |
| Otherwise | Output | Exec (any other frame) |

## Generated code

```python
_fsx_cur  = self.own.get('fsm_fsm_state', '')
_fsx_prev = getattr(self, '_fsm_fsm_xprev', None)
self._fsm_fsm_xprev = _fsx_cur
if _fsx_prev == 'idle' and _fsx_cur != 'idle':
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

The condition `prev == target and cur != target` detects the exact frame the object leaves the state.

## Frame-by-frame behavior

```
Frame 1: state = "patrol",  xprev = None     → Otherwise
Frame 2: state = "idle",    xprev = "patrol" → Otherwise  (previous state ≠ "idle")
Frame 3: state = "idle",    xprev = "idle"   → Otherwise
Frame 4: state = "chase",   xprev = "idle"   → On Exit ✓  ← exit detected
Frame 5: state = "chase",   xprev = "chase"  → Otherwise
Frame 6: state = "attack",  xprev = "chase"  → Otherwise  (previous state ≠ "idle")
```

`On Exit` fires only on frame 4 — the first frame where the state is no longer "idle".

## Typical usage

### Stop looping sound on exit

```
[On Update] → [On State Exit: FSM ID="vehicle", State="engine_on"]
                  └── On Exit ──► [BTCustomTask: stop_loop_sound('engine')]
```

### Deactivate visual effect on exit

```
[On Update] → [On State Exit: FSM ID="player", State="invisible"]
                  └── On Exit ──► [BTCustomTask:
                                       self.own.color = [1, 1, 1, 1]
                                       self.own['invisible'] = False]
```

### Save state before exit

```
[On Update] → [On State Exit: FSM ID="combat", State="attacking"]
                  └── On Exit ──► [BTCustomTask:
                                       self._bt_bb['last_attack_time'] = Range.logic.getClockTime()]
```

### Combined with On State Enter (full cycle)

```
# On entering "charging":
[On Update] → [On State Enter: FSM ID="weapon", State="charging"]
                  └── On Enter ──► [BTCustomTask: start_charge_vfx()]

# On leaving "charging":
[On Update] → [On State Exit: FSM ID="weapon", State="charging"]
                  └── On Exit ──► [BTCustomTask: stop_charge_vfx()]
```

### Transition with cleanup

```
# On any transition away from "chase":
[On Update] → [On State Exit: FSM ID="ai", State="chase"]
                  └── On Exit ──► [BTCustomTask:
                                       self.ai_move_target = None
                                       self.ai_target_pos  = None]
```

## Difference from On State Enter

| On State Enter | On State Exit |
|---------------|---------------|
| Fires when **arriving** at the state | Fires when **leaving** the state |
| `cur == target and prev != target` | `prev == target and cur != target` |
| Attribute: `_fsm_<id>_prev` | Attribute: `_fsm_<id>_xprev` |

Both nodes can coexist in the same tree monitoring the same state — they use separate attributes and do not interfere.

## Notes

- On the first frame, `_fsx_prev` is `None` — the node does not fire `On Exit` at startup even if the initial state differs from the target (because `None != target` makes the `prev == target` condition false).
- If `SetState` changes the state in the same frame that `On State Exit` runs, the node detects the change on the next frame.
- To detect the exit from **any** state (not a specific one), use [On Property Change](../event/on-property-change.md) on `self.own['fsm_<id>_state']`.
