# ANIMATION Category

Nodes for controlling Range Game Engine action animations.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Play Action](play-action.md) | Action | Plays an animation action |
| [Stop Action](stop-action.md) | Action | Stops animation on a layer |

## Animation Layer System

Range Game Engine supports multiple simultaneous animation layers. Each `Play Action` specifies a layer (0–7), enabling animation blending. For example: layer 0 for locomotion, layer 1 for arm animations, layer 2 for facial expressions.

## Play Modes

| Mode | Behavior |
|------|----------|
| PLAY | Plays once from start to end |
| LOOP | Continuous loop |
| PING_PONG | Forward and backward |
| PROPERTY | Controlled by a BGE property |
| FLIPPER | No loop (same as PLAY but different BGE term) |
| LOOPEND | Finishes the loop at the end |
| LOOPSTOP | Stops the loop |
