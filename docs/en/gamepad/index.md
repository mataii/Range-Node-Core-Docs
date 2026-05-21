# GAMEPAD Category

Nodes for reading input from connected controllers/joysticks. Support multiple devices by index.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Gamepad Connected](gamepad-connected.md) | Branch | Detects whether a gamepad is connected |
| [Gamepad Button](gamepad-button.md) | Branch | Detects button press, hold, or release |
| [Gamepad Axis](gamepad-axis.md) | Data | Returns an analog axis value with deadzone |
| [Gamepad Any Button](gamepad-any-button.md) | Branch | Detects if any button is currently pressed |

## Device Index

The `Gamepad Index` parameter (0–7) selects the device. `Range.logic.joysticks[idx]` returns `None` if no device is connected at that slot. Always use `Gamepad Connected` before reading axes or buttons in scenes where the gamepad is optional.

## Notes

- Button and axis indices depend on the OS driver and the controller model.
- "Pressed" and "released" states are computed using a previous-frame flag stored in `Range.logic.globalDict['_gp<idx>_prev']`.
