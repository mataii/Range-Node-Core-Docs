# Dialogue Speaker

**Tipo:** Dato  
**Categoría:** DIALOGUE

Devuelve el nombre del hablante de la línea actual del diálogo. Debe estar en el mismo objeto que el [Dialogue Player](dialogue-player.md) con el mismo ID.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Speaker | Salida | Dato (String) |

## Código generado

```python
getattr(self, '_dlg_spkr_dlg1', '')
```

## Uso típico

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [Set Text: Object=speaker_label, Text={Dialogue Speaker: ID="intro"}]
```

## Notas

- Devuelve cadena vacía si el hablante de la línea está vacío o el diálogo no está activo.
- Usa este nodo para mostrar el nombre del hablante en un objeto de texto separado del texto del diálogo.
