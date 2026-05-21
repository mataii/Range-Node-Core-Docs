# Tween Color

**Tipo:** Procesador / Rama (True / False)  
**Categoría:** TWEEN

Interpola el color RGBA del objeto (`self.own.color`) desde `From` hasta `To` usando los parámetros del tween indicado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Tween ID | String | `"tween"` | Debe coincidir con el ID de `Play Tween` |
| From | Vector (RGBA) | `[1,1,1,1]` | Color inicial [R, G, B, A] |
| To | Vector (RGBA) | `[1,0,0,1]` | Color final [R, G, B, A] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (completado) |
| False | Salida | Exec (en curso) |

## Código generado (simplificado)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_flash', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])
    _tw_col = [_from[i] + (_to[i] - _from[i]) * _tw_e for i in range(4)]
    self.own.color = _tw_col
    if _tw_t >= 1.0:
        #TRUE_PATH#
```

## Uso típico

### Flash rojo al recibir daño

```
[On Message: Subject="take_damage"]
    → [Play Tween: ID="hit_flash", Duration=0.3, Ease=ease_out]

[On Update] → [Tween Color:
                  ID = "hit_flash"
                  From = [2.0, 0.0, 0.0, 1.0]  # rojo brillante
                  To = [1.0, 1.0, 1.0, 1.0]]   # color normal
```

### Fade de transparencia

```
[On State Enter: FSM ID="player", State="invisible"]
    └── On Enter ──► [Play Tween: ID="fade_out", Duration=1.0, Ease=ease_in]

[On Update] → [Tween Color:
                  ID = "fade_out"
                  From = [1.0, 1.0, 1.0, 1.0]
                  To = [1.0, 1.0, 1.0, 0.0]]
```

## Notas

- `self.own.color` acepta valores >1.0 para efectos de "brillo" (emisión) en materiales que lo soporten.
- El canal alfa controla la transparencia solo si el material del objeto tiene transparencia habilitada.
- Para colorear otro objeto, usa `BTCustomTask: scene.objects['name'].color = [r,g,b,a]`.
