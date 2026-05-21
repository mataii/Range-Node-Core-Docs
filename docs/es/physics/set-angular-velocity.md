# Set Angular Velocity

**Tipo:** Acción  
**Categoría:** PHYSICS

Establece directamente la velocidad angular del objeto en radianes por segundo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.0` | Velocidad angular en eje X (rad/s) |
| Y | Float | `0.0` | Velocidad angular en eje Y (rad/s) |
| Z | Float | `0.0` | Velocidad angular en eje Z (rad/s) |
| Local | Bool | `False` | Si es True, en espacio local |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.setAngularVelocity([0.0, 0.0, 3.14], False)
```

## Uso típico

### Detener rotación de objeto

```
[On Message: Subject="freeze"] → [Set Angular Velocity: X=0, Y=0, Z=0]
```

### Girar objeto a velocidad constante

```
[On Start] → [Set Angular Velocity: Z=1.0, Local=True]
```

## Notas

- `setAngularVelocity` sobreescribe la velocidad de rotación actual.
- El amortiguamiento angular (configurado con [Set Damping](set-damping.md)) reducirá la velocidad con el tiempo.
