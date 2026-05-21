# Weapon ADS

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Implementa el sistema de "Apuntar" (Aim Down Sights). Gestiona la transición suavizada de entrada/salida al modo ADS y publica multiplicadores para FOV, retroceso, dispersión y sensibilidad que los demás nodos del pipeline consumen.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Right Mouse to Aim | Bool | `True` | Botón derecho del mouse como input ADS |
| Toggle Mode | Bool | `False` | Pulsar para entrar/salir (en lugar de mantener) |
| Instant ADS | Bool | `False` | Transición instantánea sin suavizado |
| ADS Speed | Float | `8.0` | Velocidad de la transición (alpha/s) |
| FOV Mult | Float (0.1–1.0) | `0.65` | Multiplicador de FOV a ADS completo (< 1 = zoom) |
| Recoil Mult | Float (0–1) | `0.5` | Multiplica el retroceso al apuntar |
| Spread Mult | Float (0–1) | `0.3` | Multiplica la dispersión al apuntar |
| Sensitivity Mult | Float (0.1–1) | `0.7` | Multiplica la sensibilidad del mouse al apuntar |
| Movement Slowdown | Bool | `False` | Publica `weapon_ads_move_mult` para ralentizar al jugador |
| Move Mult | Float (0.1–1) | `0.75` | Factor de velocidad de movimiento en ADS |
| Camera Offset | Bool | `False` | Publica `weapon_ads_camera_offset` (desplazamiento Z de cámara) |
| Camera Offset | Float | `0.05` | Offset Z de cámara en ADS completo |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_ads` | bool | `True` cuando el input ADS está activo |
| `weapon_ads_active` | bool | Alias de `weapon_ads` |
| `weapon_ads_alpha` | float (0–1) | Progreso de la transición ADS (0 = hip, 1 = full ADS) |
| `weapon_ads_fov_mult` | float | Multiplicador de FOV interpolado |
| `weapon_ads_recoil_mult` | float | Multiplicador de retroceso interpolado |
| `weapon_ads_spread_mult` | float | Multiplicador de dispersión interpolado |
| `weapon_ads_sensitivity_mult` | float | Multiplicador de sensibilidad interpolado |
| `weapon_ads_move_mult` | float | Multiplicador de velocidad (solo si `Movement Slowdown = True`) |
| `weapon_ads_camera_offset` | float | Offset de cámara (solo si `Camera Offset = True`) |

## Integración con otros nodos

- **Weapon Recoil** lee `weapon_ads_recoil_mult` para escalar el retroceso.
- **Weapon Spread** lee `weapon_ads_spread_mult` si `ADS Bonus` está activo.
- **Weapon Animation** usa `weapon_ads_active` para las animaciones de entrada/salida ADS.
- **Weapon FX System** suprime el flash de cañón si `Suppress in ADS` está activo.

## Posición en el grafo

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon ADS] → [Weapon Fire Executor] → ...
```
