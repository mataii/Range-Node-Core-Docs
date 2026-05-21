# TWEEN Category

Nodes for smooth property interpolation with easing functions. Allow animating position, float values, and colors without manual interpolation code.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Play Tween](play-tween.md) | Action | Starts an interpolation (writes parameters to globalDict) |
| [Tween Position](tween-position.md) | Processor/Branch | Interpolates `worldPosition` from From to To |
| [Tween Float](tween-float.md) | Processor/Branch | Interpolates a float BGE property |
| [Tween Color](tween-color.md) | Processor/Branch | Interpolates `self.own.color` RGBA |

## Available Easing Functions

| Easing | Behavior |
|--------|----------|
| `linear` | Constant speed |
| `ease_in` | Starts slow, accelerates |
| `ease_out` | Starts fast, decelerates |
| `ease_in_out` | Slow → fast → slow |
| `bounce` | Bounce at the end |

## Usage Pattern

`Play Tween` writes parameters to `globalDict['_rnc_tw_<id>']`. The corresponding `Tween*` node reads those parameters each frame, computes the interpolated value, and applies it. The Tween node branches to `True` (complete) / `False` (in progress).

```
[On State Enter] → [Play Tween: ID="door", Duration=1.0, Ease=ease_out]
[On Update] → [Tween Position: ID="door", From=[0,0,0], To=[0,0,2]]
                  └── True ──► [Set State: State="open"]
```
