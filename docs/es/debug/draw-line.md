# Draw Line

**Tipo:** Acción  
**Categoría:** DEBUG

Dibuja una línea 3D entre dos puntos en el mundo durante el frame actual. Usa `Range.render.drawLine()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| From | Vector | `[0,0,0]` | Punto de inicio en coordenadas de mundo. Si `from_self=True`, se usa la posición del objeto |
| To | Vector | `[0,0,1]` | Punto final en coordenadas de mundo. Si `to_obj` está definido, se usa su posición |
| Color | Vector (RGB) | `[1,0,0]` | Color de la línea |
| From Self | Bool | `False` | Si es True, el punto de inicio es `self.own.worldPosition` |
| To Object | String | `""` | Nombre de un objeto de escena. Si no está vacío, el punto final es su `worldPosition` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.render.drawLine([0,0,0], [0,0,1], [1,0,0])
```

Con `from_self=True` y `to_obj="target"`:

```python
_dl_from = list(self.own.worldPosition)
_dl_to   = list(scene.objects['target'].worldPosition)
Range.render.drawLine(_dl_from, _dl_to, [1,0,0])
```

## Uso típico

### Visualizar dirección de movimiento

```
[On Update] → [Draw Line:
                   from_self = True
                   To = {Get Property: prop="move_dir"}
                   Color = [0,1,0]]
```

### Trazar línea entre dos objetos

```
[On Update] → [Draw Line:
                   From Self = True
                   To Object = "enemy"
                   Color = [1,0,0]]
```

### Visualizar posición objetivo

```
[On Update] → [Draw Line:
                   From = [0,0,0]
                   To Object = "waypoint"
                   Color = [0,0,1]]
```

## Notas

- La línea solo existe durante el frame en que se ejecuta. Para verla continuamente, llama al nodo cada frame desde `On Update`.
- `Range.render.drawLine` acepta listas o tuplas de 3 floats para posición y color.
- El color no tiene canal alfa — la línea siempre es opaca.
- No afecta al rendimiento en producción de forma notable, pero debe eliminarse antes de publicar.
