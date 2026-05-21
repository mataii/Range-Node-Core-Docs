# FSM Category

The **FSM** (Finite State Machine) category provides a generic, lightweight system for managing states on any type of object: doors, levers, NPCs, pickups, UI elements, etc.

## Usage pattern

```
1. SetState      → change state from any event chain
2. OnState       → branch logic per state from an OnUpdate chain
3. OnStateEnter  → one-shot entry logic (animation, sound, VFX)
4. OnStateExit   → one-shot exit logic (cleanup, transitions)
5. GetState      → data source: current state name
6. GetStateTime  → data source: seconds spent in the current state
```

## Nodes

| Node | Type | Role |
|------|------|------|
| [Set State](set-state.md) | Exec (In → Out) | Transition to a new state |
| [On State](on-state.md) | Branch (True/False) | True while in the given state |
| [On State Enter](on-state-enter.md) | Branch (On Enter/Otherwise) | Fires one frame on state entry |
| [On State Exit](on-state-exit.md) | Branch (On Exit/Otherwise) | Fires one frame on state exit |
| [Get State](get-state.md) | Data | Returns the current state name |
| [Get State Time](get-state-time.md) | Data | Returns seconds spent in the current state |

---

## How it works internally

State is stored as a **BGE object property**:

```
self.own['fsm_<id>_state'] = "idle"
```

This lets [On Property Change](../event/on-property-change.md) react to state changes with no additional setup.

The state timer is recorded as a Python attribute:

```python
self._fsm_<id>_t = Range.logic.getClockTime()
```

`GetStateTime` returns `getClockTime() - self._fsm_<id>_t`.

---

## Multiple FSMs on the same object

Each FSM is identified by a **FSM ID** (string). If a single object needs to manage several independent states, use different IDs:

```
FSM ID = "move"     → self.own['fsm_move_state']
FSM ID = "anim"     → self.own['fsm_anim_state']
FSM ID = "dialogue" → self.own['fsm_dialogue_state']
```

All nodes belonging to the same FSM must share the same FSM ID.

---

## Full example: door

```
# Transition to "opening" on pressing E:
[On Update] → [On Key Press: E, activated]
                  └── True ──► [Set State: FSM ID="door", State="opening"]

# State logic:
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [BTCustomTask: play_anim('open')]

[On Update] → [On State Enter: FSM ID="door", State="opening"]
                  └── On Enter ──► [BTCustomTask: play_sound('door_creak')]

[On Update] → [On State Enter: FSM ID="door", State="open"]
                  └── On Enter ──► [BTCustomTask: self.own['collidable'] = False]

# After 2 seconds in "opening", move to "open":
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Get State Time: FSM ID="door"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 2.0]
                                                       └── True ──► [Set State: State="open"]
```

---

## FSM vs AI State Machine

| FSM (this category) | AI State Machine (AI category) |
|--------------------|-------------------------------|
| General purpose — any object | Specific to enemy AI |
| Customizable FSM ID | Fixed `ai_state`, `ai_prev_state` variables |
| No `execution_order` | `execution_order = 50` |
| No AI pipeline integration | Integrated with EnemyPerception, EnemyCombat, etc. |

For enemies with a full modular pipeline, use [AI State Machine](../ai/ai-state-machine.md). For any other object, use this FSM category.

---

## Notes

- `SetState` is a **no-op** if the object is already in the target state — the timer is not reset.
- The FSM ID is sanitized to a valid Python identifier (`_safe`): spaces and hyphens become `_`.
- State is a string (`str`) — any value passed through the Data socket is converted with `str()`.
- If `SetState` has never been called, `GetState` returns the configured `Default` value.
- `GetStateTime` returns `0.0` before the first `SetState` call.
