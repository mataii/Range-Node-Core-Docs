# Portal Teleport

**Tipo:** Acción  
**Categoría:** RAYCAST

Teletransporta el objeto entre dos portales al estilo del juego Portal de Valve: preserva la velocidad relativa y reorienta el objeto según la transformación del portal de salida, incluyendo el giro de 180° estándar.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Destination | Enum | `Target Object` | `XYZ Coordinates` (manual) o `Target Object` (usando portales) |
| Portal In (A) | Object | — | Objeto que representa el portal de entrada |
| Portal Out (B) | Object | — | Objeto que representa el portal de salida |
| X / Y / Z | Float | `0.0` | Destino manual (solo con `XYZ Coordinates`) |
| Launch on Exit | Bool | `True` | Redirige la velocidad lineal al nuevo sistema de referencia |
| Exit Offset | Float | `1.0` | Desplazamiento desde el portal de salida para no quedar incrustado |
| Preserve Angular Velocity | Bool | `False` | También redirige la velocidad angular |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| X / Y / Z | Entrada | Dato (Float) — destino manual, sobreescribe la propiedad |
| Out | Salida | Exec |

## Código generado (modo Target Object)

```python
# -- Portal Teleport --
import mathutils as _mu
_pt_scene = self.own.scene
_pt_b = _pt_scene.objects.get('portal_b')
if _pt_b:
    _pt_b_mat = _pt_b.worldTransform.copy()
    _pt_b_ori = _pt_b.worldOrientation.copy()
    _pt_a = _pt_scene.objects.get('portal_a')
    if _pt_a:
        _pt_a_ori = _pt_a.worldOrientation.copy()
        _pt_rel_ori = _pt_a_ori.inverted() * self.own.worldOrientation
        _pt_flip = _mu.Matrix.Rotation(3.14159265, 3, 'Z')
        _pt_new_ori = _pt_b_ori * _pt_flip * _pt_rel_ori
        self.own.worldOrientation = _pt_new_ori
        _pt_forward = _pt_b_ori * _mu.Vector((0.0, -1.0, 0.0))
        _pt_new_pos = _pt_b.worldPosition + _pt_forward * 1.0
        self.own.worldPosition = _pt_new_pos
        # Redirigir velocidad lineal
        _pt_vel = self.own.linearVelocity.copy()
        if _pt_vel.length > 0.001:
            _pt_vel_local = _pt_a_ori.inverted() * _pt_vel
            _pt_vel_new   = _pt_b_ori * (_pt_flip * _pt_vel_local)
            self.own.linearVelocity = _pt_vel_new
        else:
            self.own.linearVelocity = _mu.Vector((0, 0, 0))
```

## Uso típico

### Portal clásico entre dos objetos marcadores

```
[On Collision: Object="portal_trigger_a"]
    → [Portal Teleport:
           Destination=Target Object
           Portal In=portal_a
           Portal Out=portal_b
           Launch on Exit=True
           Exit Offset=1.2]
```

### Teletransporte simple a coordenadas

```
[On Key Press: Key=T, Mode=PRESSED]
    → [Portal Teleport:
           Destination=XYZ Coordinates
           X=10.0  Y=0.0  Z=1.0
           Portal Out=checkpoint_marker
           Launch on Exit=False]
```

## Notas

- El portal de salida (`Portal Out`) determina la orientación de llegada; es obligatorio.
- El portal de entrada (`Portal In`) es opcional — sin él, la orientación relativa no se transforma.
- `Exit Offset` evita que el objeto quede dentro de la geometría del portal al salir.
- El nodo se coloca normalmente en el objeto que viaja, no en los portales.
