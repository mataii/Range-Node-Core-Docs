# Set State

**Type:** Exec (In → Out)  
**Category:** FSM

Transitions the state machine to a new state and resets the state timer. It is a **no-op** if the object is already in the target state, which prevents the timer from resetting every frame.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | FSM identifier. Multiple FSMs on one object use different IDs |
| New State | String | `"idle"` | Target state (only when the State socket is not connected) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| State | Input | Data (String — dynamic target state) |
| Out | Output | Exec |

## Generated code

```python
_fsm_new = str("idle")
if self.own.get('fsm_fsm_state', '') != _fsm_new:
    self.own['fsm_fsm_state'] = _fsm_new
    self._fsm_fsm_t = Range.logic.getClockTime()
```

When the State socket is connected, `"idle"` is replaced by the socket expression.

## Behavior

1. Converts the target value to `str()` (accepts any type).
2. Compares with the current state in `self.own['fsm_<id>_state']`.
3. If different: writes the new state to the BGE property and records the current time.
4. If same: does nothing (no-op).

## Typical usage

### Simple transition

```
[On Key Press: E, activated]
    └── True ──► [Set State: FSM ID="door", New State="opening"]
```

### Transition from data (State socket)

```
[Get State: FSM ID="door"] ──(State)──► [Set State: FSM ID="door"]
```

Useful for copying the state from one object to another.

### Dynamic transition from blackboard

```
[BT Set Blackboard: next_state = "attack"]
...
[Set State: FSM ID="enemy", State = self._bt_bb.get('next_state', 'idle')]
```

### Chained with condition logic

```
[On Update] → [On State: FSM ID="fsm", State="patrol"]
                  └── True ──► [Distance Check: target]
                                    └── Near ──► [Set State: FSM ID="fsm", New State="chase"]
```

### Multiple FSMs on the same object

```
[Set State: FSM ID="move",   New State="running"]
[Set State: FSM ID="anim",   New State="run_anim"]
[Set State: FSM ID="combat", New State="armed"]
```

Each `Set State` writes to an independent BGE property.

## Notes

- State is written to `self.own['fsm_<id>_state']` — Range creates the property automatically on first assignment.
- The FSM ID is sanitized: `"my door"` → `"my_door"`, producing `self.own['fsm_my_door_state']`.
- The timer `self._fsm_<id>_t` uses `getClockTime()` (absolute wall clock), not `deltaTime`.
- Connecting the State socket overrides the New State property in the panel — the socket takes priority.
