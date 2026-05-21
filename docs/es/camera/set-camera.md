# Set Camera

**Tipo:** Acción  
**Categoría:** CAMERA

Cambia la cámara activa de la escena estableciendo `scene.active_camera`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Camera | String | `"Camera"` | Nombre del objeto cámara a activar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
scene.active_camera = scene.objects['Camera']
```

## Uso típico

### Cambiar a cámara de cinemática

```
[On State Enter: FSM ID="game", State="cutscene"]
    └── On Enter ──► [Set Camera: Camera="cutscene_cam"]

[On State Exit: FSM ID="game", State="cutscene"]
    └── On Exit ──► [Set Camera: Camera="player_cam"]
```

### Seleccionar cámara de objetivo al apuntar

```
[On Key Press: Key=RIGHTMOUSE, Mode=activated]
    → [Set Camera: Camera="scope_cam"]

[On Key Press: Key=RIGHTMOUSE, Mode=released]
    → [Set Camera: Camera="main_cam"]
```

## Notas

- El objeto cámara debe existir en la escena activa con ese nombre exacto.
- Cambiar la cámara activa es inmediato — en el mismo frame la vista cambia.
