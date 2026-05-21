# Dialogue Index

**Tipo:** Dato  
**Categoría:** DIALOGUE

Devuelve el índice (base 0) de la línea actual del diálogo. Útil para disparar eventos en líneas específicas o mostrar un indicador de progreso.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Index | Salida | Dato (Int) |

## Código generado

```python
getattr(self, '_dlg_idx_dlg1', 0)
```

## Uso típico

### Disparar evento en la segunda línea (índice 1)

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [BT Condition: {Dialogue Index: ID="intro"} == 1]
                                           └── True ──► [Play Action: Action="npc_gesture"]
```

## Notas

- El índice comienza en `0` para la primera línea.
- Devuelve `0` si el diálogo no está activo.
