# BT Play Animation

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Task**

Plays an animation action on the object. Returns `RUNNING` while the animation is playing, `SUCCESS` when it reaches the final frame, and `FAILURE` if no action is configured.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Action | String | `""` | Name of the BGE action to play |
| Start | Int (≥0) | `0` | Start frame |
| End | Int (≥1) | `60` | End frame |
| Layer | Int (≥0) | `1` | Range animation layer |
| Blend In | Int (≥0) | `4` | Blend-in frames |
| Loop | Bool | `False` | Loop indefinitely (always `RUNNING`) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Results

| Condition | `_bt_last_result` |
|-----------|-------------------|
| `Action` empty | `'FAILURE'` (immediate) |
| Animation playing | `'RUNNING'` |
| Current frame ≥ End − 1 | `'SUCCESS'` (resets internal flag) |
| `Loop = True` | Always `'RUNNING'` |

## Behavior

On the first frame it executes:

1. Calls `self.own.playAction(action, start, end, layer, priority=0, blendin, play_mode)`.
2. Sets `_btpa_<sid> = True` to avoid re-triggering the animation.

Subsequent frames only query `self.own.getActionFrame(layer)` until it reaches `end - 1`.

When done, `_btpa_<sid>` resets to `False` to allow the next execution.

## Typical usage

### Attack animation before applying damage

```
[BT Condition: in_attack_range]
    └── True ──► [BT Play Animation: "Attack"] → [BT Selector]
                                                      ├── Success ──► [BT Custom Task: apply_damage]
                                                      └── Failure ──► (RUNNING, waits)
```

### Death animation (play once)

```
[BT Condition: hp <= 0]
    └── True ──► [BT Play Animation: "Death", Loop=False] → [BT Sequence]
                                                                  ├── Continue ──► [BT Custom Task: end_object]
                                                                  └── Abort ──► (still dying)
```

### Looping idle animation

```
Loop = True
Action = "Idle"
```

Always returns `RUNNING` — use it in the fallback branch of a Selector.

## Notes

- `Loop = True` prevents the node from returning `SUCCESS`. If you need to end the loop externally, use **BTCustomTask** with `stopAction()`.
- The animation fires **once** even if the node executes for multiple frames — the internal flag prevents re-triggering until done.
- If another node on a different layer interrupts the animation, the frame detector may not work correctly. Use separate layers for concurrent animations.
