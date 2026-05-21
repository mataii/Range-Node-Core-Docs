# PARTICLE Category

Nodes for controlling particle systems at runtime. Allow starting, stopping, and managing particle emitters in the scene.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Start Particles](start-particles.md) | Action | Activates a particle emitter |
| [Stop Particles](stop-particles.md) | Action | Deactivates a particle emitter |
| [Spawn Particles](spawn-particles.md) | Action | Instances a particle emitter from the library |
| [Set Particle Visible](set-particle-visible.md) | Action | Shows, hides, or toggles emitter visibility |

## Notes

- `Start Particles` combines `setVisible(True)` with `reinstancePhysicsMesh()` to restart the simulation.
- `Spawn Particles` uses `scene.addObject()` — the emitter must exist in an inactive layer or the scene library.
- For one-shot effects (explosions, impacts), `Spawn Particles` with a short `lifetime` is the recommended pattern.
