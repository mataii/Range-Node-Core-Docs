# My First FPS

**Difficulty:** Beginner  
**Method A (FPS Tools):** ~15 minutes  
**Method B (Modular pipeline):** ~45 minutes

Build a working first-person or third-person controller from scratch. This tutorial covers two approaches — choose the one that fits your project.

---

## What you'll build

A playable character that:

- Moves with WASD and sprints with Shift
- Jumps with Space
- Looks around with the mouse
- Optionally tracks health, ammo, and stamina as Game Properties

<!-- screenshot: FPS view running in Range Game Engine -->

---

## Prerequisites

- Completed [Getting Started](../../getting-started.md)
- A scene with at least a floor object (static mesh with physics)

---

## Scene setup

Both methods use the same scene configuration.

### 1. Create the character object

Add a Capsule (or any mesh) to represent the player:

- **Physics type:** Character
- **Name:** `player` (recommended for clarity)

In Range: select the mesh → Properties panel → **Physics** → Physics Type → **Character**.

### 2. Add a camera

Add a Camera object (`Shift + A` → Camera):

- Position it inside the capsule at approximately **eye level** (Z ≈ 0.7 relative to the capsule center)
- Make the camera a child of the player: select the camera, then `Shift`-select the player → `Ctrl + P` → **Object**
- Rotate the camera so it faces forward (Rotation X = 90° in Range's coordinate system)
- **Name:** `fps_camera`

!!! note "Camera for Third Person"
    For a third-person setup, the camera does not need to be a child of the player — FPS Tools (Method A) manages the camera position automatically when **Perspective = Third Person**.

<!-- screenshot: scene hierarchy showing fps_camera as child of player -->

---

## Method A — FPS Tools

**Use when:** rapid prototyping, game jams, or learning what a complete FPS controller needs before building it yourself.

**Limitation:** the controller is a self-contained system. Customizing individual behaviors — a wall-run, a custom jump arc, unique input mappings beyond the available properties — requires switching to Method B.

### Step 1 — Create the NodeTree

Select the `player` object. Open the **Node Editor**, click **New** to create a NodeTree.

### Step 2 — Add the nodes

- `Shift + A` → **EVENT → On Update**
- `Shift + A` → **FPS → FPS Tools**

### Step 3 — Configure FPS Tools

Select the FPS Tools node and set the following in its properties panel:

| Property | Value | Notes |
|----------|-------|-------|
| Camera | `fps_camera` | Your camera object |
| Perspective | `First Person` | Or `Third Person` |
| Distance | `4.0` | Only used in Third Person mode |
| Walk Speed | `5.0` | m/s |
| Sprint Speed | `10.0` | m/s |
| Jump Force | `7.0` | |
| Health | `True`, Max=100 | Creates a `health` Game Property |
| Ammo | `True`, Max=30 | Creates an `ammo` Game Property |

### Step 4 — Connect

Drag from the **Exec** output of **On Update** to the **In** input of **FPS Tools**:

```
[On Update] ──Exec──► [FPS Tools]
                          Camera=fps_camera
                          Perspective=First Person
                          Walk Speed=5.0
                          Jump Force=7.0
```

### Step 5 — Register the component

1. In the Node Editor side panel (`N` → **RNC** tab), click **Info Componente**
2. In the Properties Editor → **Game** tab → **Game Components**:
   - Remove the `module` placeholder if present
   - Click **Add RNC Component** → paste the copied string → confirm
   - Click **Assign NodeTree**

If you need a refresher on this flow, see [Getting Started — Register the component](../../getting-started.md).

### Step 6 — Play

Press `P` over the 3D viewport.

**Expected result:** the character moves with WASD, sprints with Shift, jumps with Space, and the mouse controls the camera direction.

<!-- screenshot: first-person view with movement working in play mode -->

---

## Method B — Modular pipeline

**Use when:** you need precise control over individual behaviors, you are building for a production game, or you need to replace or extend specific parts of the controller.

**Advantage over Method A:** each node in the pipeline can be replaced independently. You can swap Player Movement for a custom node, add a crouch behavior between Player Jump and Player Movement, or replace Mouse Look with a cinematic camera system — without touching the other nodes.

### Understanding the pipeline

Method B splits the FPS controller into four independent responsibilities:

| Node | Responsibility | Category |
|------|---------------|----------|
| Player Input | Reads keyboard and mouse every frame, publishes `_pi_*` state | PLAYER |
| Player Movement | Applies WASD movement using `_pi_*` state | PLAYER |
| Player Jump | Applies jump physics using `_pi_*` state | PLAYER |
| Mouse Look | Rotates the character (yaw) and camera (pitch) | FPS |

Player Input runs first each frame and writes its result to `self._pi_*` instance variables (e.g., `self._pi_forward`, `self._pi_jump`, `self._pi_is_sprinting`). The movement and jump nodes read those variables. Chaining them in sequence ensures a consistent, deterministic execution order.

### Step 1 — Create the NodeTree

Select the `player` object. Open the Node Editor, click **New**.

### Step 2 — Build the movement chain

Add each node with `Shift + A`:

- **EVENT → On Update**
- **PLAYER → Player Input**
- **PLAYER → Player Movement**
- **PLAYER → Player Jump**

Connect them left to right:

```
[On Update] → [Player Input] → [Player Movement] → [Player Jump]
```

**Configure Player Input** (key bindings — defaults shown):

| Property | Default |
|----------|---------|
| Forward | `W` |
| Backward | `S` |
| Left | `A` |
| Right | `D` |
| Jump | `Space` |
| Sprint | `Left Shift` |

**Configure Player Movement:**

| Property | Value |
|----------|-------|
| Walk Speed | `5.0` |
| Sprint Speed | `10.0` |
| Physics | `Character` |

**Configure Player Jump:**

| Property | Value |
|----------|-------|
| Jump Force | `7.0` |
| Physics | `Character` |

### Step 3 — Add mouse look

Add a second `On Update` and a `Mouse Look` node:

- `Shift + A` → **EVENT → On Update** (a second one)
- `Shift + A` → **FPS → Mouse Look**

Connect them:

```
[On Update] → [Mouse Look]
                  Camera=fps_camera
                  Sensitivity=2.0
                  Clamp Pitch=1.4
```

**Configure Mouse Look:**

| Property | Value |
|----------|-------|
| Camera | `fps_camera` |
| Sensitivity | `2.0` |
| Clamp Pitch | `1.4` |
| Invert Y | `False` |

!!! note "Two On Update nodes"
    A NodeTree can contain multiple On Update nodes. Each starts an independent exec chain that runs every frame. Using two keeps movement and camera logic visually separated in the editor. There is no performance penalty.

### Step 4 — Complete node graph

```
Chain 1 — Movement
[On Update] → [Player Input] → [Player Movement] → [Player Jump]

Chain 2 — Camera
[On Update] → [Mouse Look: Camera=fps_camera]
```

<!-- screenshot: Node Editor showing both chains side by side -->

### Step 5 — Register the component

Same as Method A: **Info Componente** → **Add RNC Component** → **Assign NodeTree**.

### Step 6 — Play

Press `P`. The result is identical to Method A — same movement, same jump, same mouse look.

<!-- screenshot: first-person view in play mode — result identical to Method A -->

---

## Method A vs Method B

| Criterion | Method A (FPS Tools) | Method B (Modular) |
|-----------|---------------------|---------------------|
| Setup time | ~5 min | ~20 min |
| Add wall-run behavior | Not possible | Insert a node between Player Input → Player Movement |
| Custom jump arc | Not possible | Replace Player Jump with a custom node |
| Add a new input action | Limited to available properties | Branch after Player Input with any custom node |
| Replace the camera system | Not possible | Replace Mouse Look with any node |
| Debug individual subsystems | No visibility | Disconnect a node, add Print for intermediate state |
| Extend to local multiplayer | Very difficult | Straightforward — all nodes operate on `self` |
| Prototype → production migration | Full rebuild required | No rebuild needed |

**Rule of thumb:** start with Method A to validate the game concept. Switch to Method B before adding any mechanic that FPS Tools doesn't expose as a property.

The [Monolithic vs Modular](../patterns/monolithic-vs-modular.md) guide covers this decision in depth for all RNC systems — not just the FPS controller.

---

## Next steps

- Add an enemy that reacts to the player → [Enemy AI](enemy-ai.md)
- Add health and damage → *(coming soon)*
- Understand the architecture decision → [Monolithic vs Modular](../patterns/monolithic-vs-modular.md)
- Node references: [FPS Tools](../../fps/fps-tools.md), [Mouse Look](../../fps/mouse-look.md), [Player Input](../../player/player-input.md), [Player Movement](../../player/player-movement.md), [Player Jump](../../player/player-jump.md)
