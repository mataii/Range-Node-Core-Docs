# Save Game

**Tipo:** Acción (rama)  
**Categoría:** SAVE

Persiste el contenido completo de `Range.logic.globalDict` a un archivo en disco. Ramifica según si el guardado fue exitoso o falló.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Filename | String | `"savegame"` | Nombre base del archivo (sin extensión) |
| Extension | String | `"dat"` | Extensión del archivo |
| Slot | Int | `0` | Sufijo numérico para múltiples slots (0 = sin sufijo) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Saved | Salida | Exec |
| Error | Salida | Exec |

## Código generado

```python
# Slot=0 → sin sufijo
try:
    Range.logic.saveGlobalDict('savegame', 'dat')
    # rama Saved
except:
    # rama Error

# Slot=1 → sufijo "_1"
try:
    Range.logic.saveGlobalDict('savegame_1', 'dat')
    # rama Saved
except:
    # rama Error
```

## Uso típico

### Guardar al pulsar F5

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Save Game: Filename="savegame", Extension="dat", Slot=0]
          └── Saved ──► [Set Text: Text="Guardado"]
          └── Error ──► [Set Text: Text="Error al guardar"]
```

### Sistema de slots múltiples

```
[On Message: Subject="save_slot_1"]
    → [Save Game: Filename="save", Slot=1]
          └── Saved ──► [...]
          └── Error ──► [...]
```

## Notas

- Guarda **todo** `globalDict`, no solo claves específicas.
- El archivo se crea en la ruta del proyecto (`//`).
- Antes de llamar a este nodo, asegúrate de haber escrito todos los datos con [Set Save Data](set-save-data.md) o [Property To Save](copy-property-to-save.md).
