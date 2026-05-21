# Save Exists

**Tipo:** Acción (rama)  
**Categoría:** SAVE

Comprueba si existe un archivo de guardado en disco y ramifica la ejecución según el resultado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Filename | String | `"savegame"` | Nombre base del archivo |
| Extension | String | `"dat"` | Extensión del archivo |
| Slot | Int | `0` | Sufijo numérico del slot a comprobar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Exists | Salida | Exec |
| Not Found | Salida | Exec |

## Código generado

```python
import os as _os
_save_path = _os.path.join(Range.logic.expandPath('//'), 'savegame.dat')
if _os.path.exists(_save_path):
    # rama Exists
else:
    # rama Not Found
```

## Uso típico

### Decidir si cargar o iniciar nueva partida

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Load Game: Filename="savegame"]
          └── Not Found ──► [...]  # inicializar valores por defecto
```

### Mostrar botón "Continuar" solo si hay guardado

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Set Visibility: Object="btn_continue", Visible=True]
          └── Not Found ──► [Set Visibility: Object="btn_continue", Visible=False]
```

## Notas

- Comprueba el archivo en disco, no en memoria. Usa [Has Save Data](has-save-data.md) para comprobar claves en memoria.
- El path se construye relativo al directorio del proyecto (`//`).
