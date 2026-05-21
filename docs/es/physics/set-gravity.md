# Set Gravity

**Tipo:** Acción  
**Categoría:** PHYSICS

Establece el vector de gravedad global de la escena en Range Game Engine.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.0` | Componente X de la gravedad |
| Y | Float | `0.0` | Componente Y de la gravedad |
| Z | Float | `-9.8` | Componente Z de la gravedad |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.setGravity([0.0, 0.0, -9.8])
```

## Uso típico

### Gravedad estándar

```
[On Start] → [Set Gravity: X=0, Y=0, Z=-9.8]
```

### Gravedad lunar

```
[On Start] → [Set Gravity: X=0, Y=0, Z=-1.6]
```

### Gravedad cero (espacio)

```
[On Start] → [Set Gravity: X=0, Y=0, Z=0.0]
```

## Notas

- Afecta a todos los objetos de física de la escena simultáneamente.
- El cambio es inmediato y persiste hasta que se vuelva a llamar o se cambie de escena.
- Puede usarse con [On Key Press](../input/on-key-press.md) para mecánicas de inversión de gravedad.
