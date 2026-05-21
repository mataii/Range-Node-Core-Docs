# Set Camera

**Type:** Action  
**Category:** CAMERA

Changes the active camera of the scene by setting `scene.active_camera`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Camera | String | `"Camera"` | Name of the camera object to activate |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
scene.active_camera = scene.objects['Camera']
```

## Typical usage

### Switch to cinematic camera

```
[On State Enter: FSM ID="game", State="cutscene"]
    └── On Enter ──► [Set Camera: Camera="cutscene_cam"]

[On State Exit: FSM ID="game", State="cutscene"]
    └── On Exit ──► [Set Camera: Camera="player_cam"]
```

### Switch to scope camera when aiming

```
[On Key Press: Key=RIGHTMOUSE, Mode=activated]
    → [Set Camera: Camera="scope_cam"]

[On Key Press: Key=RIGHTMOUSE, Mode=released]
    → [Set Camera: Camera="main_cam"]
```

## Notes

- The camera object must exist in the active scene with that exact name.
- Switching the active camera is immediate — the view changes on the same frame.
