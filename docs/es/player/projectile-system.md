# Projectile System

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Comportamiento completo del proyectil en vuelo. Se coloca en el **objeto proyectil** (no en el jugador). Detecta colisiones, aplica daño, gestiona la vida útil, opciones de gravedad, pool de objetos y efectos de estela.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Damage | Float | `25.0` | Daño entregado al objeto impactado |
| Lifetime | Float | `3.0` | Segundos hasta autodestrucción |
| Destroy on Hit | Bool | `True` | Destruye/libera el proyectil al primer impacto válido |
| Property Filter | String | `""` | Solo colisiona con objetos que tengan esta propiedad BGE |
| Gravity | Bool | `False` | Aplica gravedad hacia abajo cada frame |
| Gravity Strength | Float | `9.8` | Aceleración de caída (unidades/s²) |
| Use Object Pool | Bool | `False` | Devuelve el proyectil al pool al morir en lugar de destruirlo |
| Ignore Owner | Bool | `True` | Ignora colisiones con el objeto que lo spawneó (lee `_proj_owner`) |
| Team Filter | Bool | `False` | Ignora colisiones con objetos del mismo equipo |
| Team Property | String | `"team"` | Propiedad BGE usada para identificar equipos |
| Trail FX | Bool | `False` | Spawnea y parentea un objeto de estela al nacer |
| Trail Object | String | `"ProjectileTrail"` | Nombre del objeto de estela inactivo |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas (en el objeto proyectil)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `projectile_hit` | bool | `True` el frame del impacto |
| `projectile_hit_object` | KX_GameObject | Objeto impactado |
| `projectile_hit_position` | mathutils.Vector | Posición del impacto |
| `projectile_hit_normal` | mathutils.Vector | Normal de la superficie |
| `projectile_damage` | float | Daño configurado (modificable en runtime) |

## Daño

El daño se aplica a través de `globalDict['_dmg_<nombre_objeto>']`, igual que **Hit Scan Weapon**.

## Integración con Object Pool

Cuando `Use Object Pool = True`, al morir el proyectil llama al sistema de pool en lugar de `endObject()`. El pool debe estar configurado en el objeto del **jugador** (no en el proyectil).

## Grafo del objeto proyectil

```
[OnUpdate] → [Projectile System]
                 │
                 └── (Out) → [Explosion Damage] (si es explosivo)
                           → [Weapon FX System] (si tiene FX propios)
```
