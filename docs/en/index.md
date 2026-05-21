# Range Node Core — Manual

**Range Node Core (RNC)** is an addon for **Range Game Engine** that adds a blueprint node system to the engine. It lets you program your game object logic visually by connecting nodes, without writing Python code directly.

## How it works

The addon compiles the node graph into Python code that runs as a `KX_PythonComponent` via `exec()`. Each node generates a code snippet; the compiler chains them in execution order.

## Available categories

| Category | Description |
|----------|-------------|
| **PLAYER** | Movement, jump, inventory, weapons, health, FPS game systems |
| **AI** | Perception, state machines, movement, enemy combat |
| **AI_BT** | Behavior Tree — Selector, Sequence, Task, Decorator nodes |
| **FPS** | Classic FPS controller (all-in-one legacy) |
| **PHYSICS** | Forces, impulses, physics raycasts |
| **RAYCAST** | General raycast between arbitrary points |
| **EVENT** | OnStart, OnUpdate, OnCollision, OnPropertyChange |
| **FLOW** | Branch, Gate, Delay, Timer |
| **LOGIC** | AND, OR, NOT, Compare |
| **MATH** | Arithmetic and trigonometric operations |
| **OBJECT** | Transforms, BGE properties |
| **ANIMATION** | Play Action, Stop Action |
| **AUDIO** | Play Sound, Stop Sound |
| **CAMERA** | Follow, FOV |
| **FSM** | Finite State Machine |
| **SAVE** | Data save system |
| **VEHICLE** | BGE vehicle control |
| **DIALOGUE** | Dialogue system |
| **UI** | On-screen text |
| **DEBUG** | Print, Log |

## Compiler conventions

- **Exec** nodes have `In` and `Out` sockets and are chained sequentially.
- **Branch** nodes have two exec outputs (`True/False` or custom names) and generate an `if/else` block.
- **Data** nodes are pure Python expressions; they have no exec sockets.
- Data is shared between nodes on the same object via instance variables (`self._prefix_*`).
