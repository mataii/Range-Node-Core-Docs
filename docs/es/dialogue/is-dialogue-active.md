# Is Dialogue Active

**Tipo:** Dato  
**Categoría:** DIALOGUE

Devuelve `True` si el diálogo con el ID indicado está actualmente en reproducción, `False` en caso contrario.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Active | Salida | Dato (Bool) |

## Código generado

```python
getattr(self, '_dlg_active_dlg1', False)
```

## Uso típico

### Bloquear movimiento mientras hay diálogo activo

```
[On Update]
    → [BT Condition: {Is Dialogue Active: ID="intro"} == False]
          └── True ──► [Apply Force: ...]  # solo mover si no hay diálogo
```

### Alternar visibilidad del HUD de diálogo

```
[On Update]
    → [Set Visibility:
           Object=dialogue_hud
           Visible={Is Dialogue Active: ID="intro"}]
```

## Notas

- Debe estar en el mismo objeto que el [Dialogue Player](dialogue-player.md) con el mismo ID.
- Devuelve `False` antes de que el diálogo haya sido iniciado alguna vez.
