# CAMERA Category

Nodes for controlling cameras: smooth follow, field of view, camera shake, and active camera selection.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Set Camera](set-camera.md) | Action | Changes the active camera of the scene |
| [Set FOV](set-fov.md) | Action | Changes the field of view (FOV) of a camera |
| [Camera Follow](camera-follow.md) | Action | Smoothly moves the camera toward a target |
| [Trigger Camera Shake](trigger-camera-shake.md) | Action | Triggers a camera shake effect |
| [Camera Shake](camera-shake.md) | Processor | Processes the shake frame by frame on the camera |

## Camera Shake Pattern

`Trigger Camera Shake` writes shake parameters to `globalDict`. The `Camera Shake` node must run on the camera object every frame to process and apply the random offset with fade.

```
# On the object that triggers the event:
[On Collision: Property="explosive"] → [Trigger Camera Shake: Intensity=0.5, Frames=30]

# On the camera (running every frame):
[On Update] → [Camera Shake]
```
