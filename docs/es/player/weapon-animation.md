# Weapon Animation

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Máquina de estados de animación por prioridad para el modelo del arma. Reacciona a los eventos del pipeline (`weapon_fired`, `weapon_is_reloading`, `weapon_ads_active`, etc.) y reproduce la animación correspondiente con `playAction`.

Debe colocarse en el objeto del **modelo del arma** (viewmodel), no en el objeto del jugador.

## Propiedades

### Animaciones core

| Propiedad | Por defecto | Descripción |
|-----------|---------|-------------|
| Idle | `"WeaponIdle"` | Animación en reposo |
| Fire | `"WeaponFire"` | Animación de disparo |
| Reload | `"WeaponReload"` | Animación de recarga |
| Reload Empty | `""` | Recarga desde cero balas (opcional) |
| Equip | `""` | Se reproduce una vez al inicio (opcional) |

### Animaciones de movimiento

| Propiedad | Por defecto | Descripción |
|-----------|---------|-------------|
| Walk | `""` | Mientras `_pi_is_moving` (opcional) |
| Sprint | `""` | Mientras `_pi_is_sprinting` (opcional) |

### ADS y extras

| Propiedad | Por defecto | Descripción |
|-----------|---------|-------------|
| ADS Enter | `"WeaponADSEnter"` | Transición de entrada ADS (opcional) |
| ADS Exit | `"WeaponADSExit"` | Transición de salida ADS (opcional) |
| Inspect | `""` | Animación de inspección (opcional) |

### Reproducción

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Layer | Int (0–32) | `1` | Capa de animación de Range |
| Blend In | Float | `5.0` | Frames de blend-in para todas las transiciones |
| Fire Lock | Float | `0.1` | Segundos de supresión de animaciones de menor prioridad tras un disparo |
| Loop Idle | Bool | `True` | La animación Idle se reproduce en bucle |
| Empty Reload | Bool | `False` | Usa `Reload Empty` cuando `weapon_ammo_in_mag == 0` |
| Speed | Float | `1.0` | Velocidad de reproducción (sobreescribible en runtime con `_wanim_speed`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Prioridad de animaciones

Las animaciones se evalúan en este orden (mayor prioridad primero):

1. **Equip** — Solo el primer frame, si está configurado
2. **Reload** — Mientras `weapon_is_reloading`
3. **Fire** — Frame de disparo + ventana `Fire Lock`
4. **ADS Enter** — Transición de entrada ADS
5. **ADS Exit** — Transición de salida ADS
6. **Inspect** — `weapon_inspect_requested`
7. **Sprint** — `_pi_is_sprinting`
8. **Walk** — `_pi_is_moving` y sin fire lock
9. **Idle** — Cualquier otro caso (default)

Las animaciones no configuradas (campo vacío) se omiten completamente del código generado — cero overhead.

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_anim_state` | string | Nombre de la animación activa |
| `weapon_anim_playing` | bool | `True` mientras hay una acción en reproducción |
| `weapon_anim_finished` | bool | Pulso de un frame al terminar una animación no-idle |
| `weapon_anim_busy` | bool | `True` durante equip, recarga o fire lock |

## Notas

- `_wanim_blend` y `_wanim_speed` se inicializan con los valores del nodo pero son sobreescribibles en runtime (útil para variación por arma sin recompilar).
- Los slots de animación opcionales vacíos no generan código — el compilador los descarta.
