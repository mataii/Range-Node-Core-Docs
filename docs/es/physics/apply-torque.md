# Apply Torque

**Tipo:** Acción  
**Categoría:** PHYSICS

Aplica un torque al objeto, causando rotación en los ejes especificados.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.0` | Torque en eje X |
| Y | Float | `0.0` | Torque en eje Y |
| Z | Float | `0.0` | Torque en eje Z |
| Local | Bool | `False` | Si es True, aplica en espacio local |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.applyTorque([0.0, 0.0, 10.0], False)
```

## Uso típico

### Girar objeto en física al impacto

```
[On Collision] → [Apply Torque: Z=50.0, Local=True]
```

### Efecto de inestabilidad de vehículo

```
[On Update] → [Apply Torque: X={random * 5.0}, Local=True]
```

## Notas

- `applyTorque` funciona solo en objetos con física dinámica (no estáticos, no CHARACTER).
- El torque se acumula con el amortiguamiento angular — usa [Set Damping](set-damping.md) para controlar cuánto tiempo gira.
