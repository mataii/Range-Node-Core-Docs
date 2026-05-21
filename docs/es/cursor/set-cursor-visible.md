# Set Cursor Visible

**Tipo:** Acción  
**Categoría:** CURSOR

Muestra, oculta o alterna la visibilidad del cursor del sistema usando `Range.logic.mouse.visible`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Mode | Enum | `TOGGLE` | `SHOW` = mostrar, `HIDE` = ocultar, `TOGGLE` = alternar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
# SHOW
Range.logic.mouse.visible = True

# HIDE
Range.logic.mouse.visible = False

# TOGGLE
Range.logic.mouse.visible = not Range.logic.mouse.visible
```

## Uso típico

### Ocultar cursor al iniciar juego en primera persona

```
[On Start] → [Set Cursor Visible: Mode=HIDE]
```

### Mostrar cursor al abrir menú de pausa

```
[On Message: Subject="pause_open"] → [Set Cursor Visible: Mode=SHOW]
[On Message: Subject="pause_close"] → [Set Cursor Visible: Mode=HIDE]
```

## Notas

- Ocultar el cursor no impide que se mueva — sigue siendo registrado por el sistema.
- Para capturar el movimiento del ratón sin que el cursor sea visible, combina este nodo con [Capture Mouse](capture-mouse.md).
