# Property From Save

**Tipo:** Acción  
**Categoría:** SAVE

Copia un valor de `Range.logic.globalDict` a una propiedad BGE del objeto. Equivale a [Get Save Data](get-save-data.md) seguido de [Set Property](../data/set-property.md).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Save Key | String | `"player_health"` | Clave a leer de globalDict |
| Property | Enum | — | Propiedad BGE del objeto donde escribir el valor |
| Default | String | `"0"` | Valor usado si la clave no existe en globalDict |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own['health'] = Range.logic.globalDict.get('player_health', 0)
```

## Uso típico

### Restaurar propiedades tras cargar

```
[On Start]
    → [Load Game: Filename="savegame"]
          └── Loaded ──► [Property From Save: Save Key="health",  Property=health,  Default=100]
                      ──► [Property From Save: Save Key="score",   Property=score,   Default=0]
                      ──► [Property From Save: Save Key="current_level", Property=level, Default=1]
```

## Notas

- Si la clave no existe en globalDict, se escribe el valor `Default` en la propiedad.
- Si la propiedad BGE no existe en el objeto, se crea automáticamente con el tipo del valor.
- Pareja natural de [Property To Save](copy-property-to-save.md).
