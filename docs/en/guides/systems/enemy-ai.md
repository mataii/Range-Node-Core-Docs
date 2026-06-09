# Enemy AI

**Difficulty:** Intermediate  
**Method A (Enemy AI prefab):** ~10 minutes  
**Method B (Modular system):** ~30 minutes

Build an enemy that detects the player, chases them, and attacks when close enough. This tutorial covers two approaches: a single prefab node and a modular pipeline built from individual AI nodes.

---

## What you'll build

An enemy object that:

- Idles until the player enters a configurable detection radius and field of view
- Transitions between IDLE, CHASE, and ATTACK states automatically
- Moves toward the player when detected
- Attacks when within attack range

<!-- screenshot: enemy chasing player in Range Game Engine -->

---

## Prerequisites

- Completed [Getting Started](../../getting-started.md)
- A scene with a player object (completing [My First FPS](my-first-fps.md) first is recommended)
- A scene with an enemy object — any mesh works

---

## Scene setup

Both methods use the same base configuration.

### Player object
Must exist in the scene with a recognizable name. The examples below use `player`.

### Enemy object
- Any mesh (Capsule, Cube, or a character mesh)
- **Physics type:** Dynamic (or Character for more precise collision)
- This object will receive the RNC component

---

## Method A — Enemy AI (prefab)

**Use when:** you need a functional enemy quickly, the default behaviors match your design, or you are prototyping combat before committing to a final AI architecture.

**Limitation:** behaviors are fixed. Adding custom states — patrol, cover, flee, or a Behavior Tree — requires switching to Method B.

### Step 1 — Create the NodeTree

Select the enemy object. Open the **Node Editor**, click **New**.

### Step 2 — Add the nodes

- `Shift + A` → **EVENT → On Update**
- `Shift + A` → **AI → Enemy AI**

### Step 3 — Configure Enemy AI

| Property | Value | Notes |
|----------|-------|-------|
| Target | `player` | Name of the player object in the scene |
| Detection Radius | `15.0` | Distance in units to detect the player |
| Field of View | `120.0` | Degrees of peripheral vision |
| Chase Speed | `4.0` | Movement speed while chasing |
| Attack Range | `2.0` | Distance at which the enemy attacks |
| HP | `100` | Enemy hit points |

### Step 4 — Connect

```
[On Update] ──Exec──► [Enemy AI]
                          Target=player
                          Detection Radius=15.0
                          Field of View=120.0
                          Chase Speed=4.0
                          Attack Range=2.0
                          HP=100
```

### Step 5 — Register the component

Info Componente → Add RNC Component → Assign NodeTree (same flow as [Getting Started](../../getting-started.md)).

### Step 6 — Play

Press `P`. The enemy will idle until the player enters its detection radius, then switch to CHASE state and finally ATTACK state when close enough.

<!-- screenshot: Enemy AI node active, enemy chasing player -->

---

## Method B — Modular system

**Use when:** you need custom states, multiple behavior variants, Behavior Tree integration, or per-enemy tuning of individual subsystems.

Method B exposes the same underlying logic as Method A, but as individual nodes. Each node reads and writes named attributes on `self` using a shared `ai_*` prefix (e.g., `self.ai_state`, `self.ai_dist`, `self.ai_can_see`). Nodes must run in a fixed order — define that order through your exec chain.

!!! note "Simplified modular enemy"
    This tutorial uses **Seek** for movement (direct line movement toward the target). Add [Navigation Pathfinder](../../ai/navigation-pathfinder.md) + [Navigation Follow Path](../../ai/navigation-follow-path.md) when you need obstacle-aware pathfinding with waypoints.

### The pipeline

```
[On Update]
    │
    ├─► [Enemy Perception]   → writes ai_can_see, ai_dist, ai_target_pos
    │
    ├─► [Damage Receiver]    → writes ai_hp, ai_is_dead
    │
    ├─► [AI State Machine]   → reads ai_can_see, ai_dist; writes ai_state
    │
    └─► [Enemy Decision]     → branches on ai_can_see
            │
            ├─ Visible ──► [Seek] → [Enemy Rotation] → [Enemy Combat] → [Enemy Animation]
            │
            └─ Hidden  ──►         [Enemy Rotation]                   → [Enemy Animation]
```

### Step 1 — Create the NodeTree

Select the enemy object, click **New** in the Node Editor.

### Step 2 — Add all nodes

Add each node with `Shift + A`:

| Node | Category |
|------|----------|
| On Update | EVENT |
| Enemy Perception | AI |
| Damage Receiver | AI |
| AI State Machine | AI |
| Enemy Decision | AI |
| Seek | AI |
| Enemy Rotation | AI |
| Enemy Combat | AI |
| Enemy Animation | AI |

### Step 3 — Configure each node

**Enemy Perception:**

| Property | Value |
|----------|-------|
| Target | `player` |
| Detection Radius | `15.0` |
| Field of View | `120.0` |

**Damage Receiver:**

| Property | Value |
|----------|-------|
| HP | `100` |

**AI State Machine** — uses IDLE / CHASE / ATTACK / DEAD states by default. No additional configuration required for this setup.

**Enemy Decision** — branches execution based on `ai_can_see`. No configuration required.

**Seek:**

| Property | Value |
|----------|-------|
| Target | `player` |
| Speed | `4.0` |
| Arrival Radius | `2.0` |

**Enemy Rotation:**

| Property | Value |
|----------|-------|
| Speed | `5.0` |

**Enemy Combat:**

| Property | Value |
|----------|-------|
| Attack Range | `2.0` |
| Attack Cooldown | `1.5` |
| Damage | `10` |

**Enemy Animation** — configure with the action names from your project.

### Step 4 — Connect the pipeline

Connect **On Update → Enemy Perception → Damage Receiver → AI State Machine → Enemy Decision**.

From **Enemy Decision**:

- **Visible** output → **Seek → Enemy Rotation → Enemy Combat → Enemy Animation**
- **Hidden** output → **Enemy Rotation → Enemy Animation** (the enemy continues rotating and animating even when the player is not detected)

```
[On Update] → [Enemy Perception]
                  → [Damage Receiver]
                  → [AI State Machine]
                  → [Enemy Decision]
                        │
                        ├─ Visible ──► [Seek] → [Enemy Rotation] → [Enemy Combat] → [Enemy Animation]
                        │
                        └─ Hidden  ──►          [Enemy Rotation]                  → [Enemy Animation]
```

<!-- screenshot: Node Editor showing the full modular pipeline connected -->

### Step 5 — Register the component

Info Componente → Add RNC Component → Assign NodeTree.

### Step 6 — Play

Press `P`. Behavior is identical to Method A for the standard case.

---

## Method A vs Method B

| Criterion | Method A (Enemy AI) | Method B (Modular) |
|-----------|--------------------|--------------------|
| Setup time | ~5 min | ~25 min |
| Add patrol when idle | Not possible | Replace the `Hidden` branch with a Patrol node |
| Add custom AI states | Not possible | Configure additional states in AI State Machine |
| Multiple enemy variants | Property differences only | Each variant can use a different subset of nodes |
| Behavior Tree integration | Not possible | Replace Enemy Decision with BT nodes |
| Per-frame performance tuning | No control | Set Perception Interval on Enemy Perception |
| Debug individual behaviors | No visibility | Disconnect one node, insert Print to inspect `ai_*` state |
| Obstacle-aware navigation | Built-in simplified | Add Navigation Pathfinder + Navigation Follow Path |

---

## Next steps

- Add obstacle-aware pathfinding → [Navigation Pathfinder](../../ai/navigation-pathfinder.md)
- Replace Enemy Decision with a Behavior Tree → [AI_BT category](../../ai-bt/index.md)
- Full AI pipeline reference → [AI category](../../ai/index.md)
- Node references: [Enemy AI](../../ai/enemy-ai.md), [Enemy Perception](../../ai/enemy-perception.md), [AI State Machine](../../ai/ai-state-machine.md), [Seek](../../ai/seek.md)
