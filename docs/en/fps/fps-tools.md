# FPS Tools

**Type:** Action  
**Category:** FPS

All-in-one controller for first-person and third-person games. Handles WASD movement, jumping, sprinting, crouching, mouselook, and optional inventory variables. Ideal for rapid prototyping.

## Properties

### Camera / View

| Property | Default | Description |
|----------|---------|-------------|
| Camera | — | Camera object in the scene |
| Perspective | `First Person` | `First Person` or `Third Person` |
| Distance | `4.0` | Camera-to-character distance (TPS only) |
| Sensitivity | `2.0` | Mouse sensitivity |
| Mouse Smooth | `0.7` | Mouse smoothing (1 = instant) |
| Clamp Pitch | `1.4` | Vertical pitch limit in radians |
| Invert Y | `False` | Invert mouse vertical axis |

### Movement

| Property | Default | Description |
|----------|---------|-------------|
| Physics | `Character` | `Character` (uses `walkDirection`) or `Dynamic` (uses `applyMovement`) |
| Walk Speed | `5.0` | Walking speed |
| Sprint Speed | `10.0` | Sprinting speed |
| Jump Force | `7.0` | Jump impulse |
| Acceleration | `0.25` | Acceleration/deceleration lerp |
| Air Control | `0.3` | Horizontal air control (0–1) |

### Key Bindings

| Property | Default |
|----------|---------|
| Forward | W |
| Backward | S |
| Left | A |
| Right | D |
| Jump | Space |
| Sprint | Left Shift |
| Crouch | Left Ctrl |
| Interact | E |

### Inventory Variables (optional)

| Property | Default | Description |
|----------|---------|-------------|
| Health | `True`, Max=100 | Creates BGE property `health` |
| Armor | `False`, Max=100 | Creates BGE property `armor` |
| Ammo | `True`, Max=30 | Creates BGE property `ammo` |
| Reserve | `True`, Max=90 | Creates BGE property `ammo_reserve` |
| Score | `False` | Creates BGE property `score` |
| Stamina | `False`, Max=100 | Creates BGE property `stamina`, limits sprinting |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Typical usage

### Basic FPS controller

```
[On Update] → [FPS Tools:
                   Camera=fps_camera
                   Perspective=First Person
                   Walk Speed=5.0
                   Sprint Speed=10.0
                   Jump Force=7.0
                   Health=True, Max HP=100
                   Ammo=True, Max Ammo=30]
```

## BGE properties created on init

```
self.own['health']      = 100.0  # if Health=True
self.own['ammo']        = 30     # if Ammo=True
self.own['ammo_reserve']= 90     # if Reserve=True
self.own['is_crouching']= False
self.own['is_sprinting']= False
self.own['is_grounded'] = False
```

## Notes

- Connect to `On Update` — manages mouselook and movement every frame.
- The mouse is hidden and centered automatically on the first frame.
- For additional mechanics (shooting, interaction), use [FPS Raycast](fps-raycast.md) after this node.
- For fine-grained control, use [Mouse Look](mouse-look.md) with custom movement logic instead of this node.
