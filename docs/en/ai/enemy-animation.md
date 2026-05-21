# Enemy Animation

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 60 (after Enemy Combat)

Automatically selects and plays the correct animation based on the enemy's state. Priority order: Death > Hit > Walk > Idle. Only changes the action when the animation name differs from the current one.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Idle | String | `""` | Range action name for idle state |
| Walk | String | `""` | Range action name for movement |
| Hit | String | `""` | Range action name for receiving a hit |
| Death | String | `""` | Range action name for death |
| Layer | Int (≥0) | `0` | Range animation layer |
| Blend | Float (≥0) | `4.0` | Blend-in frames when switching animations |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_is_dead` | Damage Receiver |
| `ai_took_damage` | Damage Receiver |
| `ai_is_moving` | Enemy Movement |

## Behavior

### Selection priority

```
1. ai_is_dead       → Death (play_mode=0, once)
2. _ea_timer > 0   → Hit  (continues for 0.3s)
3. ai_took_damage  → Hit  (starts 0.3s timer)
4. ai_is_moving    → Walk
5. default          → Idle
```

The animation changes **only** when the selected action name differs from `self._ea_current`. This prevents restarting the action every frame.

### Hit timer

When damage is received (`ai_took_damage = True`), the internal timer `_ea_timer` is set to 0.3 seconds. During that time, the Hit animation takes priority over Walk and Idle.

## Typical usage

### Basic configuration

```
Idle:  "Idle"
Walk:  "Walk"
Hit:   "Hit"
Death: "Death"
Layer: 0
Blend: 4.0
```

```
[Enemy Movement] → [Enemy Animation]
```

## Notes

- If an animation field is empty (`""`), that animation is skipped — the node moves to the next in the priority hierarchy.
- `play_mode=0` on Death means single playback (no loop). Other animations also use `play_mode=0` but are called again if the state changes, producing an implicit loop.
- Blend-in applies at the start of each transition, not between frames of the same animation.
- Enemy Animation doesn't manage SM state directly — it only reads `ai_is_dead`, `ai_took_damage`, and `ai_is_moving`. For state-specific animations (e.g., attack animation), add a node that reads `ai_attack_requested`.
