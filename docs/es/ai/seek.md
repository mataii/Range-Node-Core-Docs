# Seek

**Tipo:** Exec (In → Out)  
**Categoría:** AI

Mueve al objeto propietario hacia un objetivo de escena con frenado progresivo al acercarse. A diferencia de BTMoveTo, Seek usa una zona de llegada suavizada: empieza a frenar antes del radio de parada para evitar que el objeto frene en seco.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | Enum | *(objetos de escena)* | Objeto de destino |
| Speed | Float (≥0.01) | `3.0` | Velocidad máxima (unidades/s) |
| Stop Radius | Float (≥0.0) | `1.0` | Distancia a la que el objeto se detiene |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Suavizado de velocidad (1=instantáneo) |
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
| Out | Salida | Exec |

## Comportamiento

### Zona de llegada

Seek calcula una zona de frenado automática:

```
arrive_zone = max(stop_radius * 2 + 1,  stop_radius + 1)
```

Mientras la distancia al objetivo sea mayor que `stop_radius`, el nodo escala la velocidad por un factor `arrive` que va de 0 a 1:

```
arrive = clamp((distancia - stop_radius) / arrive_zone, 0, 1)
velocidad_deseada = dirección.normalizada() * speed * arrive
```

Esto produce frenado progresivo en lugar de detenerse abruptamente.

### Persistencia

El nodo almacena la velocidad actual en `self._sk_vel` (Vector3). Si el objeto se destruye y vuelve a aparecer en escena, la velocidad se reinicia.

### Target inexistente

Si el objeto Target no existe en escena, el nodo hace `pass` — no mueve, no produce error.

## Uso típico

### Seguir al jugador

```
Target: Player
Speed: 4.0
Stop Radius: 1.5
Vel Lerp: 0.15
```

```
[OnUpdate] → [Seek: Player]
```

### Seek combinado con Distance Check

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Wander]
```

### En árbol de comportamiento

```
[BT Condition: ai_can_see]
    └── True ──► [BT Custom Task: seek]
```

Dentro del BTCustomTask:
```python
diff = self._bt_bb.get('target').worldPosition - self.own.worldPosition
diff.z = 0
if diff.length > 1.5:
    self.own.applyMovement(diff.normalized() * 4.0 * Range.logic.deltaTime(), False)
self._bt_last_result = 'RUNNING'
```

## Diferencia con BTMoveTo

| Característica | Seek | BTMoveTo |
|---------------|------|---------|
| Sistema | AI nativo | BT Task |
| Frenado de llegada | Sí (zona suavizada) | No (para en seco) |
| Resultado BT | No aplica | `SUCCESS`/`FAILURE`/`RUNNING` |
| Target dinámico | Sí (re-lookup cada frame) | Solo edición |

## Notas

- El eje Z se ignora en la dirección de movimiento — el desplazamiento es siempre horizontal.
- `Vel Lerp = 1.0` produce movimiento instantáneo sin inercia. Valores bajos (0.05–0.15) generan aceleración y deceleración suaves.
- Múltiples nodos Seek en el mismo árbol comparten `self._sk_vel` — usar solo un Seek por objeto o renombrar los nodos para distinguirlos.
