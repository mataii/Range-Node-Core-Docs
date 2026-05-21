# On Key Press

**Type:** Branch (True / False)  
**Category:** EVENT

Detects keyboard input for a specific key. Branches execution based on whether the key is pressed, held, or released in the current frame.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | KeyConst | `WKEY` | Key to detect. Use the capture button to select interactively |
| Mode | Enum | `activated` | Detection mode: `activated`, `active`, or `released` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (condition met) |
| False | Output | Exec (condition not met) |

## Modes

| Mode | When it fires True |
|------|-------------------|
| `activated` | Exactly on the frame the key is first pressed |
| `active` | Every frame while the key is held down |
| `released` | Exactly on the frame the key is released |

### Frame behavior

```
Frame 1: key pressed    → activated=True,  active=True,  released=False
Frame 2: key held       → activated=False, active=True,  released=False
Frame 3: key held       → activated=False, active=True,  released=False
Frame 4: key released   → activated=False, active=False, released=True
Frame 5: key not held   → all False
```

## Generated code

```python
if Range.logic.keyboard.inputs[Range.events.WKEY].activated:
    # TRUE_PATH
else:
    # FALSE_PATH
```

For `active` mode:
```python
if Range.logic.keyboard.inputs[Range.events.WKEY].active:
```

For `released` mode:
```python
if Range.logic.keyboard.inputs[Range.events.WKEY].released:
```

## Key constant format

Keys follow the format `Range.events.<KEY>CONST`. Examples:

| Key | Constant |
|-----|----------|
| W | `WKEY` |
| Space | `SPACEKEY` |
| Left Shift | `LEFTSHIFTKEY` |
| Left Arrow | `LEFTARROWKEY` |
| Enter | `RETKEY` |
| Escape | `ESCKEY` |

## Interactive key capture

The node's properties panel includes a **capture button**. Clicking it activates a modal operator that waits for a physical key press and automatically sets the Key property — no need to know the exact constant name.

## Typical usage

### Basic movement

```
[On Update] → [On Key Press: W, active]
                  ├── True  ──► [Player Movement: forward]
                  └── False ──► (nothing)
```

### Jump on press

```
[On Update] → [On Key Press: Space, activated]
                  └── True ──► [BTCustomTask: self.own.applyImpulse([0,0,5])]
```

### Toggle with FlipFlop

```
[On Update] → [On Key Press: F, activated]
                  └── True ──► [FlipFlop]
                                   ├── A ──► [flashlight ON]
                                   └── B ──► [flashlight OFF]
```

### Multiple keys in the same tree

```
[On Update] → [On Key Press: W, active] → [move forward]

[On Update] → [On Key Press: S, active] → [move backward]

[On Update] → [On Key Press: Space, activated] → [jump]
```

Each On Update is an independent chain executing in the same frame.

## Notes

- On Key Press reads the global keyboard state — it responds regardless of which object the component belongs to.
- For simultaneous keys (e.g., Shift + W), chain two On Key Press nodes in series: both must be True for the action to fire.
- `activated` and `released` fire for exactly one frame, so they do not need a [Do Once](../flow/do-once.md) guard.
- The `active` mode fires every frame while held — combine with `deltaTime` for smooth frame-rate-independent movement.
