# Interaction System

**Type:** Exec (In → Out)  
**Category:** PLAYER

Scene object interaction system using raycasting. Detects interactable objects when pressing (or holding) a key, and optionally sends them a BGE message or modifies a property.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Range | Float | `2.5` | Maximum interaction distance (Range units) |
| Property Filter | String | `"interactable"` | BGE property that marks objects as interactable |
| Type Property | String | `"interaction_type"` | BGE property from which the interaction type is read |
| Interact Key | Enum | `E` | Interaction key |
| Cooldown | Float | `0.3` | Minimum seconds between consecutive interactions |
| Origin | Enum | `Camera` | Raycast origin (`Camera` for FPS, `Object` for AI/third person) |
| Hold to Interact | Bool | `False` | Requires holding the key for `Hold Duration` seconds |
| Hold Duration | Float | `1.0` | Seconds the key must be held |
| Auto Pickup | Bool | `False` | Automatically interacts with objects of type `Auto Type` |
| Auto Type | String | `"pickup"` | Interaction type for auto-pickup |
| Send Message | Bool | `True` | Calls `target.sendMessage()` on interaction |
| Message | String | `""` | Message subject (empty = uses value of `interaction_type`) |
| Set Property | Bool | `False` | Assigns `target['_int_triggered'] = 1` on interaction |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `interaction_available` | bool | `True` when an interactable object is in range |
| `interaction_object` | KX_GameObject | Detected object (or `None`) |
| `interaction_type` | string | Value of the `Type Property` BGE property on the object |
| `interaction_triggered` | bool | One-frame pulse when interaction completes |
| `interaction_hold_progress` | float (0–1) | Hold progress (Hold to Interact mode only) |

## Interactable object setup

Add to each scene object you want to be interactable:

1. BGE property `interactable` (or whatever you set in Property Filter)
2. BGE property `interaction_type` with the type: `"pickup"`, `"door"`, `"lever"`, etc.

## Typical usage

```
[OnUpdate] → [Interaction System] → [Out]
                 ↓ interaction_available
            [Branch] ──► True ──► [UI: Show "Press E"]
```

The BGE message sent to the interactable object can activate its own logic without needing direct references.
