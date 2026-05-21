# Set Mouse Visible

**Tipo:** Acción  
**Categoría:** CURSOR

Establece directamente la visibilidad del cursor del ratón. Versión simplificada de [Set Cursor Visible](set-cursor-visible.md) que acepta un valor booleano.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Visible | Bool | `True` | `True` = mostrar cursor, `False` = ocultar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Visible | Entrada | Dato (Bool) |

## Código generado

```python
Range.logic.mouse.visible = True
```

## Notas

- Equivalente a `Set Cursor Visible` con modo `SHOW` o `HIDE`, pero acepta un dato booleano en lugar de un enum. Útil cuando el valor de visibilidad viene de una variable.
- Para alternar (TOGGLE), usa [Set Cursor Visible](set-cursor-visible.md) con modo `TOGGLE`.
