# Player Health

**Type:** Branch (In → Alive / Dead)  
**Category:** PLAYER

Receives damage each frame through a data socket, subtracts HP, and branches execution between `Alive` and `Dead`. Optionally regenerates HP after a delay since the last damage received.

On initialization, it attempts to load saved HP from the BGE property `_sl_player_hp` on the object (integration with **Save Load System**).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Max HP | Float | `100.0` | Maximum hit points |
| Regen | Bool | `False` | Enables automatic HP regeneration |
| Rate | Float | `5.0` | HP regenerated per second |
| Delay | Float | `3.0` | Seconds without taking damage before regeneration starts |

## Sockets

| Socket | Direction | Type | Description |
|--------|-----------|------|-------------|
| In | Input | Exec | |
| Damage | Input | Data | Damage points to apply this frame (0 if not connected) |
| Alive | Output | Exec | Executes while HP > 0 |
| Dead | Output | Exec | Executes when HP ≤ 0 |

## Execution flow

```
frame
  │
  ├── If Damage > 0 and HP > 0:
  │       HP = max(0, HP - Damage)
  │       regen_timer = Delay
  │
  ├── If Regen active:
  │       regen_timer -= dt
  │       If regen_timer ≤ 0 and HP < Max HP:
  │           HP = min(Max HP, HP + Rate * dt)
  │
  └── HP > 0? → Alive : Dead
```

## Internal state (`_ph_*`)

| Variable | Type | Description |
|----------|------|-------------|
| `_ph_hp` | float | Current HP |
| `_ph_max_hp` | float | Maximum HP |
| `_ph_regen_timer` | float | Time remaining before regeneration starts |

Read these values with **Player Health State**.

## Save/load integration

On the first frame, if the BGE property `_sl_player_hp` exists on the object, HP is initialized with that value instead of `Max HP`. This allows restoring HP when loading a save with **Save Load System**.

## Example graph

```
[Damage Data] ──┐
                ▼
[OnUpdate] → [Player Health]
                ├── Alive ──► [Player Movement] → [...]
                └── Dead  ──► [Play Anim Death] → [End Game]
```
