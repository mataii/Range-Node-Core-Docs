# Enemy Combat

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 58 (after Enemy Movement and Rotation)

Manages the enemy's attack. Publishes `ai_in_attack_range` for AI State Machine to decide when to enter ATTACK, and applies damage to the target when the cooldown allows.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Attack Range | Float (≥0.1) | `2.0` | Maximum attack distance |
| Damage | Float | `10.0` | Damage per hit |
| Cooldown | Float (≥0.01) | `1.0` | Seconds between attacks |
| Attack Type | Enum | `Melee` | Melee or Ranged |

### Attack Type: Melee vs Ranged

| Type | Damage method |
|------|---------------|
| Melee | Writes directly to `globalDict['_dmg_<target>']` |
| Ranged | Also writes to `globalDict['_dmg_<target>']` if no projectile is configured |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_target_pos` | Enemy Perception |
| `ai_dist` | Enemy Perception |
| `ai_target` | Enemy Perception |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `self.ai_in_attack_range` | bool | True if `ai_dist ≤ attack_range` and position is valid |

## Behavior

Each frame:

1. Computes whether the target is in range: `ai_in_attack_range = ai_dist ≤ attack_range`.
2. If in range and cooldown has expired:
   - Resets the cooldown.
   - Writes damage to `globalDict['_dmg_' + target.name]`.
3. If not in range or cooldown active: no attack, but updates `ai_in_attack_range`.

`ai_in_attack_range` is what AI State Machine uses to decide between CHASE and ATTACK.

## Damage channel

```python
Range.logic.globalDict['_dmg_' + target.name] += damage
```

If the target has **Damage Receiver**, it will receive the damage automatically. If it has **Player Health**, also (same channel).

### Accumulated damage in the same frame

If multiple enemies attack the same target in the same frame, damage accumulates:
```python
Range.logic.globalDict.get(key, 0.0) + damage
```

## Typical usage

### Full pipeline

```
[Enemy Perception] → [AI State Machine] → [Enemy Combat] → [Enemy Animation]
```

AI State Machine sets `ai_attack_requested = True` when state is ATTACK. Enemy Combat applies damage based on its own cooldown.

### Difficulty tuning

```
Damage: 5.0
Cooldown: 2.0   # slow attack
```

```
Damage: 25.0
Cooldown: 0.5   # fast attack
```

## Notes

- Enemy Combat **does not decide** when to attack — it only manages the cooldown and the damage channel. The attack decision is made by AI State Machine (ATTACK state) via the `ai_in_attack_range` signal.
- The internal timer `_ec_time` is not reset by state changes — an attack may happen even if the state just changed to ATTACK if the cooldown had expired beforehand.
- Damage Receiver and Enemy Combat are **not compatible** on the same object (the enemy can't deal damage to itself, but if `ai_target` points to the same object, damage would arrive). Ensure `ai_target` is a different object from the owner.
