# Set Visibility

**Tipo:** Acción  
**Categoría:** OBJECT

Muestra u oculta el objeto que ejecuta el componente usando `setVisible()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Visible | Bool | `True` | `True` = mostrar, `False` = ocultar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Visible | Entrada | Dato (Bool) |

## Código generado

```python
self.own.setVisible(True)
```

## Uso típico

### Ocultar objeto al morir

```
[On Message: Subject="enemy_dead"] → [Set Visibility: Visible=False]
```

### Mostrar/ocultar según estado FSM

```
[On Update] → [On State: FSM ID="player", State="invisible"]
                  ├── True ──► [Set Visibility: Visible=False]
                  └── Otherwise ──► [Set Visibility: Visible=True]
```

## Notas

- Ocultar un objeto con `setVisible(False)` lo excluye del renderizado pero **no** lo excluye de la física ni de las colisiones.
- Para excluir de colisiones también, usa `suspendPhysics()` (disponible en la categoría PHYSICS).
- Actúa sobre `self.own` — el objeto que tiene el componente. Para ocultar otro objeto, usa `BTCustomTask` con `scene.objects['name'].setVisible(bool)`.
