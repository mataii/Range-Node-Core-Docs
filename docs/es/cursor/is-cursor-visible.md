# Is Cursor Visible

**Tipo:** Dato  
**Categoría:** CURSOR

Devuelve si el cursor del ratón es actualmente visible.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Visible | Salida | Dato (Bool) |

## Código generado

```python
Range.logic.mouse.visible
```

## Uso típico

### Condicionar lógica al estado del cursor

```
[On Update] → [BT Condition: {Is Cursor Visible}]
                  └── True ──► [...]  # cursor visible: modo UI activo
```

### Guardar estado antes de cambiar

```
[On Start] → [BTCustomTask:
                  self._bt_bb['was_cursor_visible'] = {Is Cursor Visible}]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec.
- Lee `Range.logic.mouse.visible` directamente — valor booleano del sistema.
