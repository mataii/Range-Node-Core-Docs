# Spawn At Hit

**Tipo:** Acción  
**Categoría:** FPS

Spawnea un objeto en el punto de impacto del último raycast (`_rc_pos`). Debe ejecutarse en la rama **Hit** de [FPS Raycast](fps-raycast.md) para que `_rc_pos` y `_rc_norm` estén disponibles.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Object | Enum | — | Objeto a spawnear (debe estar inactivo en la escena como template) |
| Lifetime | Int | `120` | Duración en frames antes de que el objeto spawneado se elimine (0 = permanente) |
| Align to Normal | Bool | `True` | Si es True, rota el objeto para alinearlo con la normal de la superficie |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_sah_spawned = self.own.scene.addObject('bullet_hole', self.own, 120)
if _sah_spawned: _sah_spawned.worldPosition = _rc_pos
if _rc_norm and _sah_spawned:
    _sah_spawned.worldOrientation = _rc_norm.to_track_quat('Z', 'Y').to_matrix()
```

## Uso típico

### Impacto de bala con decal

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0]
          └── Hit  ──► [Spawn At Hit: Object="bullet_decal", Lifetime=300, Align to Normal=True]
          └── Miss ──► [...]
```

### Spawnear partículas de impacto

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0]
          └── Hit  ──► [Spawn At Hit: Object="spark_particle", Lifetime=60, Align to Normal=False]
          └── Miss ──► [...]
```

## Notas

- Requiere que `_rc_pos` exista en el mismo frame — conéctalo directamente en la rama Hit de `FPS Raycast`.
- El objeto template debe estar en la escena pero oculto (no activo) para poder spawnearse con `addObject`.
- Con `Align to Normal=True`, el eje Z del objeto spawneado apunta en la dirección de la normal de la superficie.
