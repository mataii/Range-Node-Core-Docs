# Toggle

**Tipo:** Acción  
**Categoría:** LOGIC

Alterna el valor booleano de una propiedad BGE (`self.own[prop] = not self.own[prop]`).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | String | `"active"` | Nombre de la propiedad BGE a alternar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own['active'] = not self.own['active']
```

## Uso típico

### Alternar modo de linterna

```
[On Key Press: Key=F, Mode=PRESSED] → [Toggle: prop="flashlight_on"]
```

### Alternar pausa del juego

```
[On Key Press: Key=ESC, Mode=PRESSED] → [Toggle: prop="paused"]
```

### Activar/desactivar colisiones visualmente

```
[On Key Press: Key=TAB, Mode=PRESSED]
    → [Toggle: prop="debug_visible"]
    → [BTCustomTask:
           self.own.setVisible(self.own.get('debug_visible', False))]
```

## Notas

- La propiedad debe existir y ser booleana o un valor que Python pueda negar con `not`. Si no existe, lanza `KeyError`.
- Inicializa la propiedad en `On Start` si puede no existir: `[Set Property: prop="active", Value=False]`.
