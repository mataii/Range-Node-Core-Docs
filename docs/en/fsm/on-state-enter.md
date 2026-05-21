# On State Enter

**Type:** Branch (On Enter / Otherwise)  
**Category:** FSM

Fires the `On Enter` path for exactly **one frame** when the FSM enters the given state. Ideal for initialization logic: playing an animation, triggering a sound, spawning a particle effect — anything that should happen once on entry.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | Identifier of the FSM to monitor |
| State | String | `"idle"` | State whose entry is detected |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| On Enter | Output | Exec (first frame in the given state) |
| Otherwise | Output | Exec (any other frame) |

## Generated code

```python
_fse_cur  = self.own.get('fsm_fsm_state', '')
_fse_prev = getattr(self, '_fsm_fsm_prev', None)
self._fsm_fsm_prev = _fse_cur
if _fse_cur == 'idle' and _fse_prev != 'idle':
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

The condition `cur == target and prev != target` ensures it only fires on the exact frame of the transition.

## Frame-by-frame behavior

```
Frame 1: state = "patrol",  prev = None     → Otherwise
Frame 2: state = "patrol",  prev = "patrol" → Otherwise
Frame 3: state = "chase",   prev = "patrol" → Otherwise  (different state)
Frame 4: state = "idle",    prev = "chase"  → On Enter ✓  ← transition detected
Frame 5: state = "idle",    prev = "idle"   → Otherwise
Frame 6: state = "idle",    prev = "idle"   → Otherwise
```

`On Enter` fires only on frame 4 — even if the object stays in "idle" indefinitely.

## Typical usage

### Play animation on entry

```
[On Update] → [On State Enter: FSM ID="door", State="opening"]
                  └── On Enter ──► [BTCustomTask: self.own.playAction('door_open', 1, 30)]
```

### Entry sound

```
[On Update] → [On State Enter: FSM ID="enemy", State="alert"]
                  └── On Enter ──► [BTCustomTask: play_sound('alert_voice')]
```

### Initialize variables on entry

```
[On Update] → [On State Enter: FSM ID="combat", State="attacking"]
                  └── On Enter ──► [BT Set Blackboard: attack_timer = 0.0]
                                        → [BT Set Blackboard: hits_landed = 0]
```

### Multiple states with different entry logic

```
[On Update] → [On State Enter: FSM ID="pickup", State="collected"]
                  └── On Enter ──► [BTCustomTask: play_collect_sfx()]

[On Update] → [On State Enter: FSM ID="pickup", State="respawning"]
                  └── On Enter ──► [BTCustomTask: start_respawn_vfx()]
```

### Combined with On State (continuous + entry)

```
# Continuous logic while in the state:
[On Update] → [On State: FSM ID="fsm", State="burning"]
                  └── True ──► [BTCustomTask: apply_damage_over_time()]

# Entry logic (fires once):
[On Update] → [On State Enter: FSM ID="fsm", State="burning"]
                  └── On Enter ──► [BTCustomTask: play_ignite_sound()]
```

## Notes

- `_fsm_<id>_prev` is initialized to `None` on the first frame — so the first frame in any state **does** fire `On Enter` (since `None != target`). If the initial state is set by `SetState` in `On Start`, the first `On State Enter` for that state will fire on the first `On Update`.
- On State Enter and On State Exit use separate Python attributes (`_fsm_<id>_prev` vs `_fsm_<id>_xprev`) so they can coexist without interfering.
- This node has no dynamic State socket — the target state is always specified as a fixed property. For dynamic detection, use `On Property Change` on `self.own['fsm_<id>_state']`.
