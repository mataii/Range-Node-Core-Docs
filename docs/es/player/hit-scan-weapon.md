# Hit Scan Weapon

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Sistema de disparo hitscan moderno. Lanza uno o más raycasts instantáneos cuando `weapon_fired = True`, aplica daño a los objetos impactados vía `globalDict` y publica datos del impacto para FX y lógica posterior.

Soporta múltiples pellets (escopeta), filtro por propiedad BGE, filtro de equipo y trazadores opcionales.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Damage | Float | `25.0` | Daño por raycast/pellet que impacta |
| Range | Float | `100.0` | Distancia máxima del raycast (unidades de Range) |
| Pellets | Int (1–32) | `1` | Rayos por disparo (`1` = pistola/rifle, `>1` = escopeta) |
| Property | String | `""` | Filtro BGE — solo impacta objetos con esta propiedad (vacío = cualquiera) |
| Origin | Enum | `Camera` | Origen del rayo |
| Ignore Owner | Bool | `True` | Excluye el objeto propio del raycast (modos Object/Hybrid) |
| Team Filter | Bool | `False` | Ignora impactos en objetos del mismo equipo |
| Team Property | String | `"team"` | Propiedad BGE usada para identificar equipos |
| Tracer FX | Bool | `False` | Spawnea un objeto trazador en el origen del disparo |
| Tracer Object | String | `"TracerFX"` | Nombre del objeto inactivo para el trazador |
| Tracer Frames | Int | `6` | Duración del trazador en frames |

### Modos de origen

| Modo | Descripción |
|------|-------------|
| `Camera` | Rayo desde la cámara activa (FPS estándar) |
| `Object` | Rayo desde este objeto (IA / tercera persona) |
| `Hybrid` | Posición del objeto + dirección de la cámara (convergente al crosshair) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_hit` | bool | `True` si al menos un pellet impactó |
| `weapon_hit_object` | KX_GameObject | Último objeto impactado |
| `weapon_hit_position` | mathutils.Vector | Posición del último impacto |
| `weapon_hit_normal` | mathutils.Vector | Normal de la superficie impactada |

## Daño

El daño se aplica vía `globalDict` con la clave `_dmg_<nombre_objeto>`. El objeto dañado debe tener un nodo que lea esta clave (ej: **Enemy Perception**, **Player Health** con socket Damage conectado a un nodo que lea el globalDict).

## Integración con Weapon Spread

Lee `weapon_spread_pitch` y `weapon_spread_yaw` para desviar la dirección del rayo si Weapon Spread está presente.

## Posición en el grafo

```
[Weapon Spread] → [Hit Scan Weapon] → [Weapon FX System]
```
