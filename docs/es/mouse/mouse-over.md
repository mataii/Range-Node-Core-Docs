# Mouse Over

**Tipo:** Rama (Over / None)  
**Categoría:** MOUSE

Detecta si el cursor del ratón está sobre un objeto 3D usando un rayo desde la cámara activa. Permite filtrar por nombre de objeto o propiedad BGE.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target Name | String | `""` | Nombre de objeto específico. Vacío = cualquier objeto |
| Prop Filter | String | `""` | Solo detecta objetos con esta propiedad BGE. Vacío = sin filtro |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Over | Salida | Exec (cursor sobre objeto) |
| None | Salida | Exec (sin objeto bajo cursor) |

## Código generado

```python
_mo_cam = scene.active_camera
_mo_hit = _mo_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    ''  # prop_filter
)
if _mo_hit:
    #OVER_PATH#
else:
    #NONE_PATH#
```

Con `target_name`:

```python
if _mo_hit and _mo_hit.name == 'target_name':
    #OVER_PATH#
```

## Uso típico

### Resaltar objetos interactuables

```
[On Update] → [Mouse Over: Prop Filter="clickable"]
                  ├── Over ──► [BTCustomTask:
                                    # resaltar objeto bajo cursor
                                    self._bt_bb['hovered'] = _mo_hit.name]
                  └── None ──► [BTCustomTask:
                                    self._bt_bb['hovered'] = None]
```

## Notas

- El objeto hit está disponible como `_mo_hit` en el contexto del nodo.
- Similar a [Cursor Over](../cursor/cursor-over.md) — usa el mismo mecanismo `getScreenRay`.
