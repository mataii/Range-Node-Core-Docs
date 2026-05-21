# Apply Force

**Tipo:** Acción  
**Categoría:** PHYSICS

Aplica una fuerza instantánea al objeto usando `applyForce()`. La fuerza puede aplicarse en espacio local o de mundo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.0` | Componente X de la fuerza |
| Y | Float | `0.0` | Componente Y de la fuerza |
| Z | Float | `0.0` | Componente Z de la fuerza |
| Local | Bool | `False` | Si es True, aplica en espacio local del objeto |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| X, Y, Z | Entrada | Dato (Float) |

## Código generado

```python
self.own.applyForce([0.0, 0.0, 100.0], False)
```

## Uso típico

### Impulso de salto

```
[On Key Press: Key=SPACE, Mode=PRESSED] → [Apply Force: Z=500.0, Local=False]
```

### Explosión radial (BTCustomTask)

```
[On Collision: Property="explosive"] → [BTCustomTask:
                                            dir = self.own.worldPosition - explosion_pos
                                            dir.normalize()
                                            self.own.applyForce(dir * 800, False)]
```

## Notas

- La fuerza se aplica por frame si se llama en `On Update`. Para un impulso único, llama desde `On Key Press` con modo `PRESSED` o `On Start`.
- La masa del objeto afecta la aceleración resultante (F = ma).
