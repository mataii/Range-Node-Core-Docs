# Projectile Behavior (Legacy)

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

> **Deprecado.** Para proyectos nuevos usa **Projectile System** (junto con Weapon Projectile para el spawn), que separa la responsabilidad de disparo y comportamiento, y soporta trail FX y mejor integración con el pool.

Nodo que se adjunta al **objeto proyectil** (no al jugador). Gestiona su ciclo de vida: tiempo de vida, colisiones, daño y destrucción. Soporta gravedad y devolución al pool de objetos.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Damage | Float (≥0) | `25.0` | Daño aplicado al objeto golpeado |
| Lifetime | Float (≥0.1) | `3.0` | Segundos hasta auto-destrucción |
| Destroy on Hit | Bool | `True` | Destruye el proyectil al primer impacto |
| Property Filter | String | `""` | Solo colisiona con objetos que tengan esta propiedad BGE (vacío = cualquiera) |
| Gravity | Bool | `False` | Aplica caída por gravedad cada frame |
| Gravity Strength | Float (≥0) | `9.8` | Aceleración hacia abajo (unidades/s²) |
| Use Object Pool | Bool | `False` | Devuelve el proyectil al pool en lugar de llamar a `endObject()` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `projectile_hit` | bool | Pulso de un frame cuando hay colisión |
| `projectile_hit_object` | KX_GameObject | Objeto impactado (o `None`) |
| `projectile_hit_position` | Vector | Posición del impacto |
| `projectile_hit_normal` | Vector | Normal de la superficie impactada |

## Detección de colisiones

El nodo usa un raycast predictivo basado en la velocidad lineal del objeto (`worldLinearVelocity`). Cada frame lanza un rayo de longitud `speed * deltaTime + 0.1` unidades en la dirección del movimiento. Esto detecta colisiones incluso en proyectiles rápidos que podrían atravesar geometría fina en un solo frame.

## Daño

El daño se escribe en `Range.logic.globalDict['_dmg_<nombre_objeto>']`. El objeto objetivo debe leer y consumir esta clave en su propia lógica.

## Gravedad

Cuando `Gravity = True`, cada frame se resta `gravity_strength * deltaTime` a la componente Z de `worldLinearVelocity`. No usa el sistema de física del motor para la gravedad — es acumulación manual para mayor control.

## Integración con Object Pool

Cuando `Use Object Pool = True`, al destruir el proyectil el nodo:

1. Establece `_pool_active = False` en el objeto.
2. Detiene la velocidad lineal.
3. Mueve el objeto a la posición de aparcamiento (`_pool_parking_x/y/z`).
4. Llama a `suspendDynamics(True)` si el método existe.

Si el objeto no tiene `_pool_active`, usa `endObject()` como fallback.

## Posición en el grafo

Este nodo se adjunta al **objeto proyectil**, no al jugador:

```
[OnUpdate] → [Projectile Behavior (Legacy)] → [Out]
```

El spawn del proyectil lo gestiona el código de disparo del jugador (o un nodo de tipo WeaponFire en el objeto propietario).

## Migración al sistema nuevo

| Legacy | Nuevo sistema |
|--------|---------------|
| Projectile Behavior (Legacy) | Weapon Projectile (spawn) + Projectile System (comportamiento) |
| Gravity manual | Projectile System con `Use Gravity` |
| Use Object Pool | Projectile System con `Use Pool` |
| projectile_hit_* | Mismo nombre en Projectile System |
| Sin trail | Projectile System con `Trail FX` |
