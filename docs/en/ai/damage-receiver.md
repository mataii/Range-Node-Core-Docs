# Damage Receiver

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 42 (after Enemy Perception, before AI State Machine)

Manages the object's hit points and reads damage from the `_dmg_<name>` channel in `globalDict`. Publishes HP state as `ai_*` variables for the rest of the modular system.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Max HP | Float (≥1.0) | `100.0` | Maximum hit points |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_hp` | float | Current HP |
| `self.ai_hp_max` | float | Maximum HP |
| `self.ai_is_dead` | bool | True when HP ≤ 0 (stays True) |
| `self.ai_took_damage` | bool | True only the frame damage arrived |
| `self.ai_recent_damage` | bool | Alias for `ai_took_damage` |

## Damage channel

Any object can damage this enemy by writing to `globalDict`:

```python
Range.logic.globalDict['_dmg_' + enemy.name] = amount
```

The node reads and removes (`pop`) this value each frame. If nothing is in the channel, damage is 0.

### Who writes to this channel?

| Source | Channel |
|--------|---------|
| Enemy Combat (melee/ranged) | `_dmg_<target_name>` |
| Weapon Hitscan (legacy) | `_dmg_<hit_name>` |
| Weapon Balancer | `_dmg_<target_name>` |
| Custom code | `Range.logic.globalDict['_dmg_<name>'] = dmg` |

## Behavior

Each frame:

1. Reads `_dmg_<self.own.name>` from globalDict (and removes it).
2. Subtracts damage from `_dr_hp`.
3. Updates `ai_hp`, `ai_took_damage`.
4. If HP ≤ 0 and `ai_is_dead` was False: sets `ai_is_dead = True` (transition happens only once).

`ai_is_dead` never returns to False — it is a terminal state. To "revive" the enemy you must destroy and replace the object.

## Use in the modular system

```
[Enemy Perception] → [Damage Receiver] → [AI State Machine]
```

AI State Machine reads `ai_hp`, `ai_hp_max`, and `ai_is_dead` to decide whether to enter RETREAT or DEAD.

### Dealing damage to the enemy from any object

```python
# From any BTCustomTask, script, or node on another object:
enemy = self.own.scene.objects.get('Enemy_01')
if enemy:
    Range.logic.globalDict['_dmg_' + enemy.name] = 25.0
```

## Difference from Enemy AI (prefab)

| Damage Receiver | Enemy AI (prefab) |
|----------------|------------------|
| Channel: `_dmg_<name>` | Channel: `_ai_dmg_<name>` |
| Publishes `ai_*` | Does not publish external `ai_*` |
| Part of the modular system | Self-contained |

> **Do not mix** Damage Receiver with Enemy AI on the same object — they use different globalDict channels.

## Notes

- `ai_took_damage` is True for **one frame only** — the frame damage arrived. Next frame it returns to False.
- To react to damage (flash, sound, stagger), connect Enemy Hit Flash or read `ai_took_damage` in Enemy Animation.
- Accumulated damage in the same frame (multiple projectiles) works correctly because the damage channel uses `get(key, 0) + dmg` accumulation before the `pop`.
