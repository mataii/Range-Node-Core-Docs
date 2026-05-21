# Mouse Pick

**Tipo:** Acción (rama)  
**Categoría:** RAYCAST

Lanza un rayo desde la cámara activa a través de la posición del cursor del ratón. Ideal para selección de objetos 3D, apuntado de punto y clic y sistemas de interacción.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ray ID | String | `"mpick"` | Identificador compartido con Raycast Result |
| Distance | Float | `100.0` | Longitud máxima del rayo |
| Prop Filter | String | `""` | Solo impacta objetos con esta propiedad BGE |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Hit | Salida | Exec |
| Miss | Salida | Exec |

## Código generado

```python
_mp_cam  = self.own.scene.active_camera
_mp_mpos = Range.logic.mouse.position
_mp_vec  = _mp_cam.getScreenVect(_mp_mpos[0], _mp_mpos[1])
_mp_orig = _mp_cam.worldPosition.copy()
_mp_to   = _mp_orig - _mp_vec * 100.0
_mp_res  = _mp_cam.rayCast(_mp_to, _mp_orig, 100.0, '', 0, 0, 0)
self._rc_mpick_hit = _mp_res[0]
self._rc_mpick_pos = list(_mp_res[1]) if _mp_res[1] else [0.0, 0.0, 0.0]
self._rc_mpick_nrm = list(_mp_res[2]) if _mp_res[2] else [0.0, 0.0, 1.0]
if _mp_res[0] is not None:
    # rama Hit
else:
    # rama Miss
```

## Uso típico

### Resaltar objeto bajo el cursor

```
[On Update]
    → [Mouse Pick: Distance=50.0, Ray ID="hover"]
          └── Hit  ──► [BTCustomTask:
                            _hit = getattr(self, '_rc_hover_hit', None)
                            if _hit: _hit['highlighted'] = True]
          └── Miss ──► [...]
```

### Colocar objeto en el punto de clic

```
[On Mouse Button: Button=LEFT, Mode=PRESSED]
    → [Mouse Pick: Distance=200.0, Prop Filter="terrain", Ray ID="place"]
          └── Hit  ──► [Add Object:
                           Object="building"
                           X={Raycast Result: Ray ID="place", Result=Hit Pos X}
                           Y={Raycast Result: Ray ID="place", Result=Hit Pos Y}
                           Z={Raycast Result: Ray ID="place", Result=Hit Pos Z}]
          └── Miss ──► [...]
```

## Notas

- El rayo se emite desde `scene.active_camera`, no desde el objeto que tiene el componente.
- Los resultados se almacenan en `self._rc_<ID>_hit/pos/nrm` como en el resto de nodos de raycast.
- Usa [Raycast Result](raycast-result.md) con el mismo `Ray ID` para leer nombre, posición y normal.
- Combina con `Prop Filter` para solo detectar ciertos tipos de objetos (terreno, enemigos, etc.).
