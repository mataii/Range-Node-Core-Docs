# On State

**Type:** Branch (True / False)  
**Category:** FSM

Checks whether the state machine is in a specific state and branches execution. Used in [On Update](../event/on-update.md) chains to run continuous logic while the object remains in that state.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | Identifier of the FSM to query |
| State | String | `"idle"` | State to check (only when the State socket is not connected) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| State | Input | Data (String — state to compare dynamically) |
| True | Output | Exec (currently in the given state) |
| False | Output | Exec (in a different state) |

## Generated code

```python
if self.own.get('fsm_fsm_state', '') == str("idle"):
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Typical usage

### Continuous logic per state

```
[On Update] → [On State: FSM ID="door", State="open"]
                  └── True ──► [BTCustomTask: allow_player_through()]

[On Update] → [On State: FSM ID="door", State="closed"]
                  └── True ──► [BTCustomTask: block_player()]
```

### Multiple states sharing the same logic (chained)

On State does not support multiple states in one node — chain two nodes using the False path:

```
[On Update] → [On State: State="walking"]
                  ├── True  ──► [apply_movement_anim()]
                  └── False ──► [On State: State="running"]
                                    └── True ──► [apply_run_anim()]
```

### With state timer

```
[On Update] → [On State: FSM ID="charge", State="charging"]
                  └── True ──► [Get State Time: FSM ID="charge"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 3.0]
                                                       └── True ──► [Set State: State="fire"]
```

### Dynamic state (State socket)

```
[BTCustomTask: _target_state = self._bt_bb.get('mode', 'idle')]
    → [On State: FSM ID="fsm", State=_target_state]
```

## Difference from On State Enter / Exit

| On State | On State Enter | On State Exit |
|----------|---------------|---------------|
| True **every frame** in that state | True **only on the first frame** of entry | True **only on the first frame** of exit |
| For continuous logic | For initialization (sounds, animations) | For cleanup (deactivating effects) |

## Notes

- If `SetState` has never been called, `self.own.get('fsm_<id>_state', '')` returns `''` — no state will match until the first transition.
- The State socket converts the value to `str()`, the same as `SetState`.
- Connecting the State socket overrides the State property in the panel.
- Multiple `On State` nodes for the same FSM ID each check their own state independently in the same frame.
