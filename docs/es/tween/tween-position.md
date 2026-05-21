# Tween Position

**Tipo:** Procesador / Rama (True / False)  
**Categoría:** TWEEN

Interpola `worldPosition` del objeto desde `From` hasta `To` usando los parámetros del tween identificado por `ID`. Bifurca a `True` cuando completa.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Tween ID | String | `"tween"` | Debe coincidir con el ID de `Play Tween` |
| From | Vector | `[0,0,0]` | Posición de inicio |
| To | Vector | `[0,0,1]` | Posición final |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (tween completado) |
| False | Salida | Exec (tween en curso) |

## Código generado (simplificado)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_door', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])  # aplica easing
    _tw_pos = [_from[i] + (_to[i] - _from[i]) * _tw_e for i in range(3)]
    self.own.worldPosition = _tw_pos
    if _tw_t >= 1.0:
        #TRUE_PATH#
    else:
        #FALSE_PATH#
```

## Uso típico

### Puerta que se eleva

```
# Al entrar en estado "opening":
[On State Enter: FSM ID="door", State="opening"]
    └── On Enter ──► [Play Tween: ID="door", Duration=1.5, Ease=ease_out]

# Cada frame:
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Tween Position: ID="door", From=[0,0,0], To=[0,0,3]]
                                    └── True ──► [Set State: State="open"]
```

### Teleportación suave

```
[On Message: Subject="teleport_to_B"]
    → [Play Tween: ID="tp", Duration=0.5, Ease=ease_in_out]

[On Update] → [Tween Position: ID="tp",
                               From={Object Position: Object="point_A"}
                               To={Object Position: Object="point_B"}]
```

## Notas

- Si `globalDict['_rnc_tw_<id>']` no existe, el nodo cae a `False` sin error.
- Los vectores `From` y `To` pueden ser coordenadas fijas o conectarse desde sockets de datos.
