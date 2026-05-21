# Set Parent

**Tipo:** Acción  
**Categoría:** OBJECT

Establece el padre del objeto actual en la jerarquía de la escena.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Parent | String | `"parent_obj"` | Nombre del nuevo objeto padre |
| Compound | Bool | `False` | Si es True, combina la física con el padre |
| Ghost | Bool | `False` | Si es True, el hijo es fantasma (sin colisiones propias) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.setParent(scene.objects['parent_obj'], False, False)
```

## Uso típico

### Recoger objeto (adjuntar a mano del jugador)

```
[On Collision: Object="Player"] → [Set Parent: Parent="hand_R"]
```

### Adjuntar escudo a personaje

```
[On Message: Subject="equip_shield"] → [Set Parent: Parent="arm_L", Compound=True]
```

## Notas

- `Compound=True` une las físicas del hijo con las del padre — útil para objetos equipables.
- `Ghost=True` hace el hijo no colisionable mientras es hijo.
- Para desadjuntar, usa [Remove Parent](remove-parent.md).
