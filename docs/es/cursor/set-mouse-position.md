# Set Mouse Position

**Tipo:** Acción  
**Categoría:** CURSOR

Mueve el cursor del ratón a una posición específica en la pantalla usando coordenadas normalizadas [0.0, 1.0].

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| X | Float | `0.5` | Posición horizontal [0.0 = izquierda, 1.0 = derecha] |
| Y | Float | `0.5` | Posición vertical [0.0 = arriba, 1.0 = abajo] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| X | Entrada | Dato (Float) |
| Y | Entrada | Dato (Float) |

## Código generado

```python
Range.logic.mouse.position = (0.5, 0.5)
```

## Uso típico

### Mover cursor a un elemento de UI

```
[On Message: Subject="show_menu"] → [Set Mouse Position: X=0.5, Y=0.3]
                                   → [Set Cursor Visible: Mode=SHOW]
```

### Teleportar cursor al centro al abrir inventario

```
[On Key Press: Key=TAB, Mode=PRESSED]
    → [Set Mouse Position: X=0.5, Y=0.5]
    → [Set Cursor Visible: Mode=SHOW]
```

## Notas

- Equivalente directo a `Center Cursor` cuando se usan los valores `X=0.5, Y=0.5`, pero permite posiciones arbitrarias.
- El sistema operativo puede ignorar el cambio de posición si la ventana no tiene el foco.
