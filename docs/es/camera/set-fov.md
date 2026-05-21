# Set FOV

**Tipo:** Acción  
**Categoría:** CAMERA

Cambia el campo de visión de una cámara modificando la propiedad `lens` del objeto cámara.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Camera | String | `""` | Nombre del objeto cámara. Vacío = `scene.active_camera` |
| FOV | Float | `50.0` | Longitud focal en mm (menor = mayor ángulo de visión) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| FOV | Entrada | Dato (Float) |

## Código generado

```python
scene.active_camera.lens = 50.0
```

Con objeto específico:

```python
scene.objects['scope_cam'].lens = 200.0
```

## Uso típico

### Zoom al apuntar (ADS)

```
[On Key Press: Key=RIGHTMOUSE, Mode=activated]
    → [Set FOV: FOV=25.0]  # zoom in

[On Key Press: Key=RIGHTMOUSE, Mode=released]
    → [Set FOV: FOV=50.0]  # FOV normal
```

### FOV dinámico según velocidad

```
[On Update] → [Set FOV:
                   FOV = {Math: A=50, B={Vehicle Speed: m_s * 0.5}, Op=ADD}]
```

## Notas

- `lens` es la longitud focal en mm, no el ángulo en grados. Usa: `FOV_degrees = 2 * atan(sensor_size / (2 * lens))`.
- Valores típicos: 35mm = FOV ancho, 50mm = normal, 100mm+ = telefoto/zoom.
- Para animaciones suaves de FOV, combina con [BT Custom Task](../ai-bt/bt-custom-task.md) que interpole el valor cada frame.
