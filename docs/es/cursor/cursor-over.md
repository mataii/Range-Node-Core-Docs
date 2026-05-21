# Cursor Over

**Tipo:** Rama (Over / None)  
**Categoría:** CURSOR

Detecta si el cursor del ratón está sobre un objeto 3D usando un rayo desde la cámara activa. Equivalente a un raycast a través de la posición del cursor.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target Object | String | `""` | Nombre de objeto específico a detectar. Vacío = cualquier objeto |
| Prop Filter | String | `""` | Solo detecta objetos con esta propiedad BGE. Vacío = sin filtro |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Over | Salida | Exec (cursor sobre el objetivo) |
| None | Salida | Exec (cursor no sobre el objetivo) |

## Código generado

```python
_co_cam = scene.active_camera
_co_ray = _co_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    'interactable'  # prop_filter, o "" para sin filtro
)
if _co_ray and (_co_ray.name == 'button_ok' or not 'button_ok'):
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Resaltar objeto al pasar el cursor

```
[On Update] → [Cursor Over: Prop Filter="interactable"]
                  ├── Over ──► [BTCustomTask: scene.objects[_co_ray.name].color = [1.5,1.5,1.5,1]]
                  └── None ──► [BTCustomTask: ...]  # restaurar colores
```

### Activar objeto al hacer click sobre él

```
[On Update] → [Cursor Over: Target Object="button_start"]
                  └── Over ──► [Mouse Button: Button=Left, Mode=PRESSED]
                                    └── True ──► [Scene Switch: Scene="Game"]
```

## Notas

- Usa `getScreenRay()` de la cámara activa — el objeto debe estar en línea de visión desde la cámara.
- Si `Target Object` está vacío y `Prop Filter` está vacío, detecta cualquier objeto con geometría.
- El objeto golpeado está disponible como `_co_ray` en el contexto del nodo (para código personalizado en `BT Custom Task`).
