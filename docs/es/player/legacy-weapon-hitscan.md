# Weapon Hitscan (Legacy)

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

> **Deprecado.** Para proyectos nuevos usa **Hit Scan Weapon**, que soporta múltiples orígenes, pellets, trazadores y modos híbridos.

Lanza un raycast desde la cámara o el objeto cuando `weapon_fired = True` y publica los datos del impacto. Aplica daño al objeto golpeado mediante el canal `globalDict`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Damage | Float (≥0) | `25.0` | Daño por impacto exitoso |
| Range | Float (≥0.1) | `100.0` | Distancia máxima del raycast (unidades de Range) |
| Property | String | `""` | Filtro BGE: solo impacta objetos que tengan esta propiedad (vacío = cualquiera) |
| Camera Origin | Bool | `True` | `True` = raycast desde la cámara activa (FPS); `False` = desde el objeto |
| Ignore Self | Bool | `True` | Omite el objeto propietario al raycastar (solo cuando `Camera Origin = False`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_hit` | bool | `True` durante el frame del impacto |
| `weapon_hit_object` | KX_GameObject | Objeto impactado (o `None`) |
| `weapon_hit_position` | Vector | Posición del impacto en el mundo |
| `weapon_hit_normal` | Vector | Normal de la superficie impactada |

## Daño

El daño se escribe en `Range.logic.globalDict['_dmg_<nombre_objeto>']`. El objeto objetivo debe leer y consumir esta clave en su propia lógica para aplicar el daño.

## Dispersión

Si **Weapon Spread** está en el grafo antes de este nodo, lee `weapon_spread_pitch` y `weapon_spread_yaw` (en radianes) y desvía el raycast acordemente.

## Posición en el grafo

```
[Weapon Fire] → [Weapon Hitscan (Legacy)] → [Weapon FX (Legacy)]
```

## Migración al sistema nuevo

| Legacy | Nuevo sistema |
|--------|---------------|
| Weapon Hitscan (Legacy) | Hit Scan Weapon |
| `weapon_hit_*` | Mismo nombre en Hit Scan Weapon |
| Sin pellets | Hit Scan Weapon con `Pellets > 1` |
| Sin trazador | Hit Scan Weapon con `Tracer FX` |
