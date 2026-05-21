# SAVE

Nodos para gestionar datos persistentes en Range Game Engine. Los datos se almacenan en `Range.logic.globalDict` (memoria) y pueden persistirse a disco mediante Save Game / Load Game.

## Nodos

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Set Save Data](set-save-data.md) | Acción | Escribe un valor en globalDict con una clave |
| [Get Save Data](get-save-data.md) | Dato | Lee un valor de globalDict por clave |
| [Has Save Data](has-save-data.md) | Acción (rama) | Comprueba si una clave existe en globalDict |
| [Delete Save Data](delete-save-data.md) | Acción | Elimina una clave de globalDict |
| [Save Game](save-game.md) | Acción (rama) | Persiste globalDict a un archivo en disco |
| [Load Game](load-game.md) | Acción (rama) | Carga un archivo de guardado a globalDict |
| [Save Exists](save-exists.md) | Acción (rama) | Comprueba si existe un archivo de guardado |
| [Property To Save](copy-property-to-save.md) | Acción | Copia una propiedad BGE a globalDict |
| [Property From Save](copy-property-from-save.md) | Acción | Copia un valor de globalDict a una propiedad BGE |
| [Clear Save Data](clear-save-data.md) | Acción | Borra todo el contenido de globalDict |

## Flujo típico de guardado

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Property To Save: Property=health, Save Key="health"]
    → [Property To Save: Property=score,  Save Key="score"]
    → [Save Game: Filename="savegame", Slot=0]
          └── Saved ──► [Set Text: Text="¡Guardado!"]
          └── Error ──► [Set Text: Text="Error al guardar"]
```

## Notas

- `globalDict` es compartido por todos los objetos de la sesión — actúa como memoria global.
- Al llamar `Save Game`, todo el contenido de `globalDict` se escribe a disco.
- Al llamar `Load Game`, se reemplaza el contenido actual de `globalDict` con el del archivo.
