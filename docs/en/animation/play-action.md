# Play Action

**Type:** Action  
**Category:** ANIMATION

Plays an animation action on the object using Range Game Engine's `playAction()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Action | String | `"action_name"` | Name of the action to play |
| Start Frame | Int | `1` | Start frame |
| End Frame | Int | `60` | End frame |
| Layer | Int | `0` | Animation layer (0–7) |
| Priority | Int | `0` | Layer priority (lower number = higher priority) |
| Blend In | Float | `0.0` | Transition frames between animations |
| Play Mode | Enum | `PLAY` | See modes in category index |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.playAction('idle', 1, 60, layer=0, priority=0, blendin=0.0, play_mode=0)
```

## Typical usage

### Idle animation in wait state

```
[On State Enter: FSM ID="player", State="idle"]
    └── On Enter ──► [Play Action:
                          Action = "player_idle"
                          Start Frame = 1
                          End Frame = 80
                          Play Mode = LOOP]
```

### Shoot animation on separate layer

```
[Weapon Fire Executor] → [Play Action:
                              Action = "arms_shoot"
                              Layer = 1
                              Blend In = 2.0]
```

### Death animation (play once)

```
[On State Enter: FSM ID="player", State="dead"]
    └── On Enter ──► [Play Action:
                          Action = "player_die"
                          Play Mode = PLAY]
```

## Notes

- Each animation layer plays its action independently. Higher layers may mask lower ones depending on armature configuration.
- `Blend In = 0.0` is an immediate cut. Values of 3–10 are smooth for most transitions.
- Action names must exactly match those in the blend file.
