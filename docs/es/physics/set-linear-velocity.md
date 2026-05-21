# Set Linear Velocity

**Tipo:** Acción  
**Categoría:** PHYSICS

Establece directamente la velocidad lineal del objeto.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.0` | Velocidad en X |
| Y | Float | `0.0` | Velocidad en Y |
| Z | Float | `0.0` | Velocidad en Z |
| Local | Bool | `False` | Si es True, aplica en espacio local |
| Preserve Z | Bool | `False` | Si es True, mantiene la velocidad Z actual |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| X, Y, Z | Entrada | Dato (Float) |

## Código generado

```python
# Preserve Z = False
self.own.setLinearVelocity([0.0, 5.0, 0.0], False)

# Preserve Z = True
_vel = list(self.own.linearVelocity)
self.own.setLinearVelocity([0.0, 5.0, _vel[2]], False)
```

## Uso típico

### Mover personaje con velocidad precisa

```
[On Update] → [Set Linear Velocity: Y=5.0, Local=True, Preserve Z=True]
```

### Detener objeto bruscamente

```
[On Message: Subject="stop"] → [Set Linear Velocity: X=0, Y=0, Z=0]
```

### Proyectil con velocidad inicial

```
[On Start] → [Set Linear Velocity: Y=20.0, Local=True]
```

## Notas

- A diferencia de `applyForce`, `setLinearVelocity` sobreescribe la velocidad actual en lugar de acumularla.
- `Preserve Z=True` es clave para personajes que necesitan moverse horizontalmente sin afectar la caída/salto.
