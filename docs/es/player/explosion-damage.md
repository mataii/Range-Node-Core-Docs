# Explosion Damage

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Aplica daño radial con falloff a todos los objetos dentro de un radio al momento de detonación. Opcionalmente aplica un impulso físico alejando los objetos del centro de la explosión.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Radius | Float | `5.0` | Radio de la explosión (unidades de Range) |
| Max Damage | Float | `100.0` | Daño máximo al centro del radio (distancia 0) |
| Min Damage | Float | `0.0` | Daño mínimo en el borde del radio |
| Property Filter | String | `""` | Solo daña objetos con esta propiedad BGE |
| Trigger | Enum | `On Spawn` | Cuándo se activa la explosión |
| Allow Retrigger | Bool | `False` | Permite que la explosión se dispare más de una vez |
| Apply Impulse | Bool | `False` | Empuja físicamente los objetos |
| Impulse Force | Float | `500.0` | Fuerza máxima del impulso en el centro |
| Line of Sight | Bool | `False` | Omite objetos bloqueados por geometría |

### Modos de disparo (Trigger)

| Modo | Descripción |
|------|-------------|
| `Projectile Hit` | Se activa cuando `projectile_hit = True` |
| `Manual` | Se activa cuando `explosion_requested = True` |
| `On Spawn` | Se activa una sola vez en el primer frame de ejecución |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Falloff de daño

El daño se interpola linealmente entre `Max Damage` (distancia 0) y `Min Damage` (distancia = Radius):

```
daño = Max Damage - (Max Damage - Min Damage) * (distancia / Radius)
```

El daño se aplica vía `globalDict['_dmg_<nombre_objeto>']` para cada objeto en el radio.

## Uso típico

Colocar en el objeto del proyectil explosivo, después de Projectile System:

```
[OnUpdate] → [Projectile System]
                 └── Out → [Explosion Damage: Trigger=Projectile Hit]
```

O en un objeto de granada que explota al spawnearse:

```
[OnUpdate] → [Explosion Damage: Trigger=On Spawn]
```
