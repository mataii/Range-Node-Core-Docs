# Weapon FX (Legacy)

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

> **Deprecado.** Para proyectos nuevos usa **Weapon FX System**, que añade soporte para humo, demora de casquillo y FX por tipo de superficie mediante Surface Material.

Spawnea objetos de FX en respuesta a `weapon_fired` (flash de cañón, casquillo) y `weapon_hit` (impacto). Gestiona temporizadores para las variables de estado `weapon_muzzle_active` y `weapon_impact_active`.

## Propiedades

### Flash de cañón

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Muzzle Flash | Bool | `True` | Activa el flash de cañón |
| Muzzle Object | String | `"MuzzleFX"` | Nombre del objeto inactivo en la escena |
| Muzzle Duration | Float (≥0.01) | `0.05` | Segundos que `weapon_muzzle_active` permanece `True` |
| Muzzle Lifetime | Int (≥1) | `3` | Duración del objeto spawneado en frames |
| Suppress in ADS | Bool | `True` | No spawnea flash mientras `weapon_ads_active = True` |

### Casquillo

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Shell Eject | Bool | `False` | Activa la expulsión de casquillos |
| Shell Object | String | `"ShellFX"` | Nombre del objeto inactivo en la escena |
| Shell Lifetime | Int (≥1) | `120` | Duración del objeto spawneado en frames |

### Impacto

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Impact FX | Bool | `True` | Activa los FX de impacto |
| Impact Object | String | `"ImpactFX"` | Nombre del objeto inactivo en la escena |
| Impact Duration | Float (≥0.01) | `0.1` | Segundos que `weapon_impact_active` permanece `True` |
| Impact Lifetime | Int (≥1) | `30` | Duración del objeto spawneado en frames |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_fx_active` | bool | `True` si muzzle o impact están activos |
| `weapon_muzzle_active` | bool | `True` durante `Muzzle Duration` tras un disparo |
| `weapon_impact_active` | bool | `True` durante `Impact Duration` tras un impacto |

## Requisitos de escena

Todos los objetos FX deben existir como **inactivos** en la escena antes de iniciar el juego. El nodo los activa con `scene.addObject()` pasando la duración en frames como tiempo de vida.

El objeto de impacto se posiciona automáticamente en `weapon_hit_position` y se alinea con `weapon_hit_normal` (requiere Weapon Hitscan antes en el grafo).

## Posición en el grafo

```
[Weapon Hitscan (Legacy)] → [Weapon FX (Legacy)]
```

## Migración al sistema nuevo

| Legacy | Nuevo sistema |
|--------|---------------|
| Weapon FX (Legacy) | Weapon FX System |
| Sin smoke | Weapon FX System con `Smoke FX` |
| Sin shell delay | Weapon FX System con `Shell Delay` |
| Sin surface FX | Weapon FX System con `Surface FX` + Surface Material |
