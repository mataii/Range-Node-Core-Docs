# LIGHT Category

Nodes for controlling lights at runtime. Allow modifying intensity, color, range, and visibility of scene light objects.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Set Light Energy](set-light-energy.md) | Action | Changes the intensity of a light |
| [Set Light Color](set-light-color.md) | Action | Changes the RGB color of a light |
| [Set Light Range](set-light-range.md) | Action | Changes the range/influence distance of a light |
| [Toggle Light](toggle-light.md) | Action | Turns a light on, off, or toggles it |
| [Get Light Energy](get-light-energy.md) | Data | Returns the current intensity of a light |

## Usage Pattern

All light nodes target a scene object by name. If `Light Object` is empty, they use `self.own` (the object running the component must itself be a light).

```
[On Start] → [Set Light Energy: Light Object="lamp_main", Energy=2.5]
[On Update] → [Toggle Light: Light Object="lamp_flicker", Mode=TOGGLE]
```

## Notes

- The `energy`, `color`, and `distance` attributes correspond to `KX_LightObject` properties in Range Game Engine.
- Color values are floats in the [0.0, 1.0] range per channel.
