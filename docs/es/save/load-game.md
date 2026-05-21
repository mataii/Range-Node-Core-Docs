# Load Game

**Tipo:** Acción (rama)  
**Categoría:** SAVE

Carga un archivo de guardado y reemplaza el contenido de `Range.logic.globalDict` con los datos del archivo. Ramifica según si la carga fue exitosa o falló.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Filename | String | `"savegame"` | Nombre base del archivo (sin extensión) |
| Extension | String | `"dat"` | Extensión del archivo |
| Slot | Int | `0` | Sufijo numérico del slot a cargar (0 = sin sufijo) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Loaded | Salida | Exec |
| Error | Salida | Exec |

## Código generado

```python
try:
    Range.logic.loadGlobalDict('savegame', 'dat')
    # rama Loaded
except:
    # rama Error
```

## Uso típico

### Cargar al iniciar el juego

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Load Game: Filename="savegame"]
                                └── Loaded ──► [Property From Save: Save Key="health", Property=health]
                                └── Error  ──► [Set Text: Text="Error al cargar"]
          └── Not Found ──► [...]  # primera sesión
```

## Notas

- `Load Game` **sobreescribe** todo `globalDict` con el contenido del archivo.
- Si necesitas combinar datos sin sobreescribir, carga antes de escribir nuevos datos.
- Comprueba la existencia del archivo con [Save Exists](save-exists.md) antes de cargar para evitar el branch Error.
