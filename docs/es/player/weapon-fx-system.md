# Weapon FX System

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Sistema moderno de efectos visuales de arma. Reactivo a los eventos del pipeline — spawnea objetos de FX para flash de cañón, humo, casquillos e impacto. Soporta FX diferenciados por tipo de superficie cuando se usa junto a **Surface Material**.

## Propiedades

### Flash de cañón

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Muzzle Flash | Bool | `True` | Activa el flash de cañón |
| Muzzle Object | String | `"MuzzleFX"` | Objeto inactivo en la escena |
| Muzzle Duration | Float | `0.05` | Segundos que `weapon_muzzle_active` permanece `True` |
| Muzzle Frames | Int | `3` | Duración del objeto spawneado en frames |
| Suppress in ADS | Bool | `True` | No spawnear flash mientras se apunta |

### Humo

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Smoke FX | Bool | `False` | Activa el humo del cañón |
| Smoke Object | String | `"SmokeFX"` | Objeto inactivo en la escena |
| Smoke Frames | Int | `60` | Duración del objeto spawneado en frames |

### Casquillo

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Shell Eject | Bool | `False` | Activa la expulsión de casquillos |
| Shell Object | String | `"ShellFX"` | Objeto inactivo en la escena |
| Shell Frames | Int | `120` | Duración del objeto spawneado en frames |
| Shell Delay | Float | `0.05` | Segundos tras el disparo antes de expulsar el casquillo |

### Impacto

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Impact FX | Bool | `True` | Activa los FX de impacto |
| Impact Object | String | `"ImpactFX"` | Objeto inactivo por defecto |
| Impact Duration | Float | `0.1` | Segundos que `weapon_impact_active` permanece `True` |
| Impact Frames | Int | `30` | Duración del objeto spawneado en frames |
| Surface FX | Bool | `False` | Usa FX diferentes por tipo de superficie (requiere Surface Material) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_muzzle_active` | bool | `True` durante `Muzzle Duration` tras un disparo |
| `weapon_impact_active` | bool | `True` durante `Impact Duration` tras un impacto |
| `weapon_fx_active` | bool | `True` si muzzle o impact están activos |

## Posición en el grafo

```
[Hit Scan Weapon / Weapon Projectile] → [Weapon FX System]
```

Todos los objetos FX deben existir como **inactivos** en la escena antes de iniciar el juego.

## Integración con Surface Material

Cuando `Surface FX = True`, lee `surface_fx_name` y `surface_audio_name` publicados por **Surface Material** para elegir el objeto de impacto correcto según el tipo de superficie golpeada.
