# Patrol

**Tipo:** Exec (In → Out / Done)  
**Categoría:** AI

Mueve al objeto propietario en secuencia entre 2–8 waypoints de escena. Soporta modo Loop (cíclico) y modo no-loop (para al llegar al último waypoint). Incluye frenado de llegada al acercarse a cada waypoint.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Waypoints | Int (2–8) | `2` | Número de waypoints activos |
| WP 1 … WP N | Enum | *(objetos de escena)* | Waypoints en orden de visita |
| Speed | Float (≥0.01) | `2.0` | Velocidad de patrulla (unidades/s) |
| Arrive Radius | Float (≥0.1) | `1.0` | Distancia para considerar que llegó al waypoint |
| Loop | Bool | `True` | Volver al WP1 al terminar el recorrido |
| Physics | Enum | `Character` | Modo de física del objeto |

### Modos de física

| Modo | Método de movimiento |
|------|----------------------|
| `Character` | `applyMovement()` en espacio de mundo |
| `Dynamic` | Asigna `localLinearVelocity.x/y` (preserva Z) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec (continua) |
| Done | Salida | Exec (cuando Loop=False y llegó al último WP) |

## Comportamiento

### Frenado de llegada

Igual que Seek, Patrol usa una zona de frenado automática antes de cada waypoint:

```
arrive_zone = max(arrive_radius * 3, arrive_radius + 1.5)
arrive = clamp((distancia - arrive_radius) / arrive_zone, 0, 1)
```

Esto evita que el objeto frene de golpe al llegar.

### Estado persistente

El nodo mantiene `self._pt_idx` (índice del waypoint actual) y `self._pt_wps` (lista de nombres). Se inicializan solo la primera vez.

### Loop = True

Al llegar al último waypoint, el índice vuelve a 0:

```python
self._pt_idx = (self._pt_idx + 1) % len(self._pt_wps)
```

### Loop = False

Al llegar al último waypoint, el índice no avanza más. En el siguiente frame, el socket `Done` se activa.

## Uso típico

### Patrulla de tres puntos en bucle

```
Waypoints: 3
WP 1: Waypoint_A
WP 2: Waypoint_B
WP 3: Waypoint_C
Speed: 2.0
Loop: True
```

```
[OnUpdate] → [Patrol] → [Out: idle animations / other logic]
                 │
                Done: (nunca si Loop=True)
```

### Patrulla lineal sin loop

```
Loop: False
[OnUpdate] → [Patrol]
                 │
                Done ──► [BT Custom Task: end patrol behavior]
```

### Cambiar entre Patrol y Chase

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Seek: Player]       # perseguir al jugador
    └── Far  ──► [Patrol]             # patrullar si está lejos
```

## Notas

- El nodo filtra waypoints vacíos — si WP 3 está vacío y Waypoints=4, usará solo los 3 primeros que tengan nombre.
- Si todos los waypoints son inválidos, el nodo hace `pass` sin error.
- La patrulla es siempre en el plano XY — el eje Z se ignora en la dirección de movimiento.
- El índice `_pt_idx` persiste mientras el objeto exista. Para reiniciar la patrulla desde el principio, usa un BTCustomTask: `self._pt_idx = 0`.
- La velocidad de física Character se multiplica por `deltaTime()` internamente; la de Dynamic no (es una velocidad directa).
