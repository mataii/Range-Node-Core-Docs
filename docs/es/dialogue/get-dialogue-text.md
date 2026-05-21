# Dialogue Text

**Tipo:** Dato  
**Categoría:** DIALOGUE

Devuelve el texto de la línea actual del diálogo. Debe estar en el mismo objeto que el [Dialogue Player](dialogue-player.md) con el mismo ID.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Text | Salida | Dato (String) |

## Código generado

```python
getattr(self, '_dlg_text_dlg1', '')
```

## Uso típico

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [Set Text: Object=text_box, Text={Dialogue Text: ID="intro"}]
```

## Notas

- Devuelve cadena vacía si el diálogo no está activo o no se ha iniciado.
- Solo actualiza cuando `Dialogue Player` cambia de línea — no es necesario leerlo en `Idle`.
