# Weapon Projectile

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Spawnea y configura un objeto proyectil en la escena cuando `weapon_fired = True`. Aplica la dirección, velocidad y offset de dispersión al proyectil recién instanciado. La lógica de colisión, daño y vida útil corresponde al nodo **Projectile System** colocado en el objeto proyectil.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Object Name | String | `"Projectile"` | Nombre del objeto inactivo en la escena |
| Speed | Float | `50.0` | Velocidad inicial del proyectil (unidades/s) |
| Lifetime | Int | `180` | Duración del proyectil en frames |
| Spawn Offset | Float | `1.0` | Distancia frontal desde el origen antes de posicionar el proyectil |
| Active Duration | Float | `0.1` | Segundos que `weapon_projectile_active` permanece `True` tras el spawn |
| Camera Direction | Bool | `True` | Usa la dirección de la cámara activa (FPS). Desactivar para origen del objeto (IA) |
| Inherit Velocity | Bool | `False` | Suma la velocidad lineal del objeto al proyectil |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_projectile_spawned` | bool | Pulso de un frame al spawnear el proyectil |
| `weapon_projectile_active` | bool | `True` durante `Active Duration` segundos tras el spawn |

## Integración con Weapon Spread

Lee `weapon_spread_pitch` y `weapon_spread_yaw` para desviar la dirección de lanzamiento si Weapon Spread está presente.

## Requisitos del objeto proyectil

- Debe existir como objeto **inactivo** en la escena.
- Debe tener física activa (Dynamic o Character) para detectar colisiones.
- El comportamiento de vuelo y colisión se configura con **Projectile System** en el propio objeto proyectil.

## Posición en el grafo

```
[Weapon Spread] → [Weapon Projectile] → [Weapon FX System]
```

El objeto proyectil tiene su propio grafo RNC con:
```
[OnUpdate] → [Projectile System]
```
