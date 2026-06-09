# Monolithic vs Modular

RNC offers two ways to build any major game system. Understanding when to use each approach is one of the most important architectural decisions in a Range project.

---

## The two approaches

**Monolithic nodes** package an entire system into a single node. You configure it through properties and it handles everything internally.

Examples: `FPS Tools`, `Enemy AI`, `Vehicle Player`, `Drone AI`.

```
[On Update] → [FPS Tools: Camera=fps_camera, Walk Speed=5.0, Jump Force=7.0]
```

**Modular pipelines** break the same system into individual nodes, each responsible for one behavior. Nodes communicate through `self.*` instance variables using shared prefixes (`_pi_*`, `ai_*`, `weapon_*`, etc.).

Examples: `Player Input + Player Movement + Player Jump + Mouse Look`, `Enemy Perception + AI State Machine + Enemy Decision + Seek + Enemy Combat`.

```
[On Update] → [Player Input] → [Player Movement] → [Player Jump]
[On Update] → [Mouse Look]
```

Both approaches compile to the same underlying `KX_PythonComponent` execution model. The difference is purely in how logic is organized and what can be changed independently.

---

## When monolithic wins

Use a monolithic node when:

**You are prototyping.** Monolithic nodes reach a playable state in minutes. Validate the game concept before committing to an architecture. A game jam project, a pitch demo, or a proof-of-concept belongs here.

**The default behaviors match your design exactly.** If FPS Tools covers every movement mechanic you need, adding architectural complexity produces no benefit. Simple games benefit from simple architectures.

**The system is isolated.** If the system doesn't interact with custom logic and won't need to change, a monolithic node is easier to maintain and reason about.

**You are learning the system.** Building with monolithic nodes first shows you what a complete pipeline needs to do. Switching to modular becomes easier when you already know the end goal.

---

## When modular wins

Use a modular pipeline when:

**You need to replace one behavior.** Want a custom jump arc that eases out at the apex? Replace Player Jump with a custom node — Player Input and Player Movement remain unchanged.

**You need to insert behavior between existing steps.** Wall-run, crouch, ice sliding — these fit naturally as nodes between Player Input and Player Movement. Monolithic nodes have no insertion points.

**You have multiple variants of the same system.** A Heavy enemy and a Scout enemy can share Enemy Perception and AI State Machine while using different Seek configurations, different Enemy Combat cooldowns, and different Enemy Animation sets. With a monolithic node, each variant requires a separate property configuration with no structural reuse.

**You need to debug individual behaviors.** In a modular pipeline, disconnect one node and insert a Print to inspect intermediate `ai_*` or `_pi_*` state. In a monolithic node, all state is internal and invisible.

**You plan to add a Behavior Tree.** BT nodes plug into the modular AI pipeline at the Enemy Decision position. They cannot replace a monolithic Enemy AI node.

**You are building for production.** Modular pipelines survive scope changes. Adding a sprint resource cost, a stamina gate, or a dash move is a structural change in a monolithic controller but a single node insertion in a modular one.

---

## Decision criteria

| If you need to... | Use |
|---|---|
| Get something running today | Monolithic |
| Ship in a game jam | Monolithic |
| Prototype a concept before knowing what mechanics it needs | Monolithic first |
| Add a mechanic the node doesn't support as a property | Modular |
| Tune individual subsystems independently | Modular |
| Reuse parts across different object types | Modular |
| Integrate with Behavior Trees | Modular |
| Debug a specific behavior in isolation | Modular |
| Build for a game with evolving scope | Modular |

---

## Can you mix both?

Yes — and this is often the right answer for a game in active development.

A common pattern: use a monolithic node for prototyping, then replace it with a modular pipeline as the design clarifies. Because both approaches publish and read the same variable protocol (`ai_*`, `_pi_*`, `weapon_*`), components built for one remain compatible with others that follow the same protocol.

**Example — mixed scene:** a game keeps `Enemy AI` for simple enemy types (guards, turrets) and builds a full modular pipeline only for the boss enemy that needs a custom Behavior Tree and three distinct movement phases. Both coexist in the same scene without conflict because they run on separate objects.

**What to avoid:** do not run both a monolithic node and its modular equivalents on the **same object**. Both write to overlapping instance variables and will conflict on the same frame. For example, running `FPS Tools` and `Player Movement` together will create a conflict because both try to write `self.own.walkDirection` every frame.

---

## Migrating from monolithic to modular

When you decide to replace a monolithic node with a modular pipeline:

1. **Read the reference page** for the monolithic node to understand what properties and behaviors it encapsulates. The Game Systems tutorial for that system lists the equivalent modular nodes (see [My First FPS — Method A vs B](../systems/my-first-fps.md#method-a-vs-method-b), [Enemy AI — Method A vs B](../systems/enemy-ai.md#method-a-vs-method-b)).

2. **Build the modular pipeline on a test object** before replacing the original. Confirm that the observable behavior — movement speed, jump height, detection radius, attack timing — is identical.

3. **Match configuration values.** Copy the property values from the monolithic node (Walk Speed, Jump Force, Detection Radius, etc.) to the corresponding modular nodes. The variable protocol guarantees behavioral compatibility when values match.

4. **Register a new component.** The modular NodeTree requires its own Assign NodeTree step even if it lives on the same object that previously used the monolithic node.

5. **Remove the old component** from Game Components only after the modular pipeline is confirmed working in play mode.

---

## Related

- [My First FPS — Method A vs B](../systems/my-first-fps.md#method-a-vs-method-b)
- [Enemy AI — Method A vs B](../systems/enemy-ai.md#method-a-vs-method-b)
- Node references: [FPS Tools](../../fps/fps-tools.md), [PLAYER category](../../player/index.md), [AI category](../../ai/index.md)
