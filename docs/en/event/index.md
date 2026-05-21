# EVENT Category

The **EVENT** category contains the node tree entry points and engine event detectors. These are the nodes that **start** execution or trigger it in response to something that happens in the scene.

## Three groups

### Entry Points

Nodes with no In socket. They are the root of the tree — the compiler generates a separate Python method for each one.

| Node | When it executes |
|------|-----------------|
| [On Start](on-start.md) | Once when the component is created |
| [On Update](on-update.md) | Every frame |

### Event detectors

Nodes with an In socket that branch execution based on whether an event occurred:

| Node | Type | Detected event |
|------|------|----------------|
| [On Key Press](on-key-press.md) | Branch (True/False) | Key pressed / held / released |
| [On Collision](on-collision.md) | Branch (Hit/None) | Collision with another object |
| [On Mouse Move](on-mouse-move.md) | Branch (Moving/Still) | Cursor movement |
| [On Timer](on-timer.md) | Branch (Elapsed/Waiting) | Internal countdown |
| [On Property Change](on-property-change.md) | Branch (Changed/Same) | BGE property value change |

### Message bus

A pair of nodes for communication between objects without direct references:

| Node | Role |
|------|------|
| [On Message](on-message.md) | Receiver — waits for a message with a subject |
| [Node Broadcast](node-broadcast.md) | Sender — sends the message to all receivers |

---

## When to use each

```
One-time startup           → On Start
Every frame                → On Update
Keyboard input             → On Key Press
Physical collision         → On Collision
Mouse movement             → On Mouse Move
Internal periodic event    → On Timer
React to BGE property      → On Property Change
Cross-object communication → On Message + Node Broadcast
```

## Multiple entry points in the same tree

A tree can have both On Start and On Update (and multiple of each). The compiler generates a separate method for each:

```python
def start(self):
    # → nodes connected to On Start

def update(self):
    # → nodes connected to On Update
```

All other nodes (detectors, FLOW, AI…) must be connected to one of these entry points to execute.

## On Timer vs Delay (FLOW)

| On Timer | Delay (FLOW) |
|----------|-------------|
| Own entry point | Requires OnUpdate as root |
| Countdown with `deltaTime()` | Absolute clock `getClockTime()` |
| Exposes Progress socket (0→1) | No progress |
| Repeat = True by default | Repeat = False by default |

## On Message vs native sendMessage()

`Node Broadcast` + `On Message` use `globalDict` — they work between **RNC Python components**. Range's native `sendMessage()` uses the Logic Bricks system and is not compatible with this bus.
