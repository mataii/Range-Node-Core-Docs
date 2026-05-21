# Weapon Fire (Legacy)

**Type:** Exec (In → Out)  
**Category:** PLAYER

> **Deprecated.** This node belongs to the original weapon pipeline. For new projects use **Weapon Fire Executor** together with Weapon State Publisher, Weapon Reload, and Weapon Balancer.

Controls weapon firing in Semi-Auto or Auto mode. In Semi, delegates to Player Input (`_pi_fire`). In Auto, manages its own fire rate timer and synthesizes fire pulses.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mode | Enum | `Semi-Auto` | `Semi-Auto` (one shot per press) or `Auto` (continuous fire while held) |
| Fire Rate | Float (0.1–100) | `10.0` | Shots per second (Auto mode only) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

**Semi-Auto:** The node compiles to `pass`. Firing depends entirely on the `_pi_fire` pulse from Player Input (`.activated` of the left mouse button).

**Auto:** The node manages `_wf_cd` (cooldown in seconds). While the left button is held (`.active`) and cooldown is ≤ 0, it forces `self._pi_fire = True` and resets the cooldown to `1 / Fire Rate`. Otherwise it forces `self._pi_fire = False`.

## Graph position

```
[Player Input] → [Weapon Fire] → [Inventory Manager] → ...
```

## Migration to the new system

| Legacy | New system |
|--------|------------|
| Weapon Fire (Semi) | Weapon Fire Executor with `Mode = Semi` |
| Weapon Fire (Auto) | Weapon Fire Executor with `Mode = Auto` + `Fire Rate` |
| — | Weapon Fire Executor with `Mode = Burst` (new) |
