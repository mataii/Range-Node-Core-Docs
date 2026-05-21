# Property To Save

**Tipo:** Acción  
**Categoría:** SAVE

Copia el valor de una propiedad BGE del objeto a `Range.logic.globalDict`. Equivale a [Set Save Data](set-save-data.md) con el valor de la propiedad como entrada.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | Enum | — | Propiedad BGE del objeto a copiar |
| Save Key | String | `""` | Clave en globalDict (vacío = mismo nombre que la propiedad) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['health'] = self.own.get('health', None)
```

## Uso típico

### Guardar múltiples propiedades antes de salvar

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Property To Save: Property=health]
    → [Property To Save: Property=score]
    → [Property To Save: Property=level,  Save Key="current_level"]
    → [Save Game: Filename="savegame"]
```

## Notas

- Si `Save Key` está vacío, la clave en globalDict es el mismo nombre de la propiedad.
- Si la propiedad no existe en el objeto, almacena `None`.
- Para cargar de vuelta, usa [Property From Save](copy-property-from-save.md).
