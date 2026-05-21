# Get Direction

**Tipo:** Dato  
**Categoría:** MATH

Devuelve una componente (X, Y o Z) de la dirección normalizada desde el objeto actual hasta un objeto objetivo. Opcionalmente aplana el vector al plano XY.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| To | String | `"target"` | Objeto objetivo |
| Component | Enum | `X` | Componente a devolver: `X`, `Y`, `Z` |
| Flat | Bool | `False` | Si es True, ignora la diferencia de Z antes de normalizar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Component | Salida | Dato (Float) |

## Código generado

```python
# Component=X, Flat=False
_gdir = (scene.objects['target'].worldPosition - self.own.worldPosition).normalized()
_gdir[0]  # componente X

# Flat=True: fuerza Z=0 antes de normalizar
_gdir_raw = scene.objects['target'].worldPosition - self.own.worldPosition
_gdir_raw[2] = 0
_gdir = _gdir_raw.normalized()
_gdir[0]
```

## Uso típico

### Mover IA hacia el jugador (componente X)

```
[On Update] → [BTCustomTask:
                   dx = {Get Direction: To="Player", Component=X, Flat=True}
                   dy = {Get Direction: To="Player", Component=Y, Flat=True}
                   self.own.applyMovement([dx * speed, dy * speed, 0], False)]
```

## Notas

- La dirección normalizada tiene longitud 1.0 — devuelve componentes en rango [-1.0, 1.0].
- Con `Flat=True`, el vector tiene Z=0 y se normaliza en XY — útil para IA de suelo que no debe inclinarse.
- Si los dos objetos están en la misma posición exacta, `.normalized()` puede devolver `Vector(0,0,0)`.
