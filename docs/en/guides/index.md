# Guides

The [reference documentation](../player/index.md) tells you what each node does. These guides tell you how to combine nodes to build real game systems.

!!! tip "New to RNC?"
    If you haven't built anything with RNC yet, start with [Getting Started](../getting-started.md) before coming here.

---

## What's in Guides

**[Game Systems](systems/index.md)**  
Step-by-step tutorials for complete game systems. Each tutorial covers two approaches: a monolithic approach for fast prototyping and a modular approach for scalable production projects.

**[Architecture Patterns](patterns/index.md)**  
Decision guides for choosing the right approach when you already know the nodes but need to understand the trade-offs.

---

## Learning paths

### Path A — Working prototype today
[Getting Started](../getting-started.md) → [FPS Prototype](../templates/fps-prototype.md) → [My First FPS](systems/my-first-fps.md) (Method A)

### Path B — Learn the system properly
[Getting Started](../getting-started.md) → [My First FPS](systems/my-first-fps.md) → [Enemy AI](systems/enemy-ai.md) → [Monolithic vs Modular](patterns/monolithic-vs-modular.md)

### Path C — Design review for an existing project
[Monolithic vs Modular](patterns/monolithic-vs-modular.md)

---

## Before you start

These guides assume you have completed [Getting Started](../getting-started.md) and are comfortable with:

- Creating a NodeTree and adding nodes with `Shift + A`
- Registering a component: Info Componente → Add RNC Component → Assign NodeTree
- The difference between exec nodes (have `In`/`Out` sockets) and data nodes (no exec sockets)
