# Get Save Data

**Tipo:** Dato  
**Categoría:** SAVE

Lee un valor de `Range.logic.globalDict` por clave. Si la clave no existe, devuelve el valor por defecto configurado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Key | String | `"player_health"` | Clave en globalDict |
| Default | String | `"0"` | Valor devuelto si la clave no existe |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (tipo del valor almacenado) |

## Código generado

```python
Range.logic.globalDict.get('player_health', 0)
```

## Uso típico

### Mostrar vida guardada en el HUD

```
[On Start]
    → [Load Game: Filename="savegame"]
          └── Loaded ──► [Set Text: Text={Get Save Data: Key="health", Default=100}]
```

### Restaurar posición del jugador

```
[On Start]
    → [Transform:
           X={Get Save Data: Key="checkpoint_x", Default=0}
           Y={Get Save Data: Key="checkpoint_y", Default=0}
           Z={Get Save Data: Key="checkpoint_z", Default=1}]
```

## Notas

- Si la clave no existe (p. ej. primera sesión sin guardado), devuelve el valor `Default`.
- El tipo del valor devuelto depende de lo que se guardó; úsalo con el tipo correcto en el socket de destino.
- Para leer una clave y copiarla directamente a una propiedad BGE, usa [Property From Save](copy-property-from-save.md).
