# Weapon Animation

**Type:** Exec (In → Out)  
**Category:** PLAYER

Priority-based animation state machine for the weapon model. Reacts to pipeline events (`weapon_fired`, `weapon_is_reloading`, `weapon_ads_active`, etc.) and plays the corresponding animation with `playAction`.

Must be placed on the **weapon model** object (viewmodel), not on the player object.

## Properties

### Core animations

| Property | Default | Description |
|----------|---------|-------------|
| Idle | `"WeaponIdle"` | Resting animation |
| Fire | `"WeaponFire"` | Fire animation |
| Reload | `"WeaponReload"` | Reload animation |
| Reload Empty | `""` | Reload from zero bullets (optional) |
| Equip | `""` | Plays once on startup (optional) |

### Movement animations

| Property | Default | Description |
|----------|---------|-------------|
| Walk | `""` | While `_pi_is_moving` (optional) |
| Sprint | `""` | While `_pi_is_sprinting` (optional) |

### ADS and extras

| Property | Default | Description |
|----------|---------|-------------|
| ADS Enter | `"WeaponADSEnter"` | ADS enter transition (optional) |
| ADS Exit | `"WeaponADSExit"` | ADS exit transition (optional) |
| Inspect | `""` | Inspect animation (optional) |

### Playback

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Layer | Int (0–32) | `1` | Range animation layer |
| Blend In | Float | `5.0` | Blend-in frames for all transitions |
| Fire Lock | Float | `0.1` | Seconds of lower-priority animation suppression after firing |
| Loop Idle | Bool | `True` | Idle animation loops continuously |
| Empty Reload | Bool | `False` | Use `Reload Empty` when `weapon_ammo_in_mag == 0` |
| Speed | Float | `1.0` | Playback speed (overridable at runtime with `_wanim_speed`) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Animation priority

Animations are evaluated in this order (highest priority first):

1. **Equip** — First frame only, if configured
2. **Reload** — While `weapon_is_reloading`
3. **Fire** — Fire frame + `Fire Lock` window
4. **ADS Enter** — ADS enter transition
5. **ADS Exit** — ADS exit transition
6. **Inspect** — `weapon_inspect_requested`
7. **Sprint** — `_pi_is_sprinting`
8. **Walk** — `_pi_is_moving` and no fire lock
9. **Idle** — Any other case (default)

Unconfigured animations (empty field) are completely omitted from generated code — zero overhead.

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_anim_state` | string | Name of the active animation |
| `weapon_anim_playing` | bool | `True` while an action is playing |
| `weapon_anim_finished` | bool | One-frame pulse when a non-idle animation ends |
| `weapon_anim_busy` | bool | `True` during equip, reload, or fire lock |

## Notes

- `_wanim_blend` and `_wanim_speed` are initialized from node values but can be overridden at runtime (useful for per-weapon variation without recompiling).
- Empty optional animation slots generate no code — the compiler discards them.
