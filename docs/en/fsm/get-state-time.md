# Get State Time

**Type:** Data  
**Category:** FSM

Data node that returns how many seconds the FSM has been in its current state. The counter resets automatically every time `SetState` transitions to a new state.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | Identifier of the FSM to query |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Seconds | Output | Data (Float) |

## Generated code

```python
(Range.logic.getClockTime() - getattr(self, '_fsm_fsm_t', Range.logic.getClockTime()))
```

Uses `getClockTime()` (absolute wall clock) — not affected by frame rate or `deltaTime`.

## Behavior

- Returns `0.0` before the first `SetState` call (because `getattr` returns `getClockTime()` as fallback, and the subtraction is ≈ 0).
- The counter advances in real time while the object remains in the same state.
- Resets to `0.0` every time `SetState` transitions to a different state.
- Does **not** reset if `SetState` is called with the same state (no-op).

## Typical usage

### Automatic transition by time

```
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Get State Time: FSM ID="door"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 2.0]
                                                       └── True ──► [Set State: State="open"]
```

### Attack cooldown

```
[On Update] → [On State: FSM ID="combat", State="idle"]
                  └── True ──► [Get State Time: FSM ID="combat"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 1.5]
                                                       └── True ──► [Set State: State="attacking"]
```

### Alert timeout

```
# If in "alert" for more than 10s without seeing the player, return to "patrol":
[On Update] → [On State: FSM ID="ai", State="alert"]
                  └── True ──► [Get State Time: FSM ID="ai"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 10.0]
                                                       └── True ──► [Set State: State="patrol"]
```

### Progress bar / loading

```
[On Update] → [On State: FSM ID="loader", State="loading"]
                  └── True ──► [BTCustomTask:
                                    t = {Get State Time: FSM ID="loader"}
                                    progress = min(1.0, t / 5.0)  # loads in 5 seconds
                                    self.own['load_bar'] = progress]
```

### Multi-phase animation

```
[On Update] → [On State: FSM ID="boss", State="intro"]
                  └── True ──► [Get State Time: FSM ID="boss"]
                                    ──(Seconds)──► [BTCustomTask:
                                                        if Seconds < 2.0:
                                                            play_phase_1()
                                                        elif Seconds < 4.0:
                                                            play_phase_2()]
                                                   → [BT Condition: Seconds >= 5.0]
                                                       └── True ──► [Set State: State="combat"]
```

## Notes

- `Get State Time` uses `getClockTime()` — this is real-world seconds, not game-scaled time. If the game is paused by modifying Range's time scale, this counter keeps advancing.
- The value is recalculated every frame — there is no intermediate buffer, it is always `now - start`.
- For simple timeout conditions, this is cleaner than maintaining a manual timer with `deltaTime`.
- If `SetState` has never been called, the value is approximately `0.0` (subtraction of two near-simultaneous `getClockTime()` calls).
