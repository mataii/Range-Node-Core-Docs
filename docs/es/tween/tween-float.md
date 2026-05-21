# Tween Float

**Tipo:** Procesador / Rama (True / False)  
**Categoría:** TWEEN

Interpola una propiedad BGE flotante del objeto desde `From` hasta `To` usando los parámetros del tween con el `ID` indicado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Tween ID | String | `"tween"` | Debe coincidir con el ID de `Play Tween` |
| Property | String | `"value"` | Nombre de la propiedad BGE a animar |
| From | Float | `0.0` | Valor inicial |
| To | Float | `1.0` | Valor final |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (completado) |
| False | Salida | Exec (en curso) |

## Código generado (simplificado)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_hp', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])
    self.own['hp_bar'] = 0.0 + (1.0 - 0.0) * _tw_e
    if _tw_t >= 1.0:
        #TRUE_PATH#
```

## Uso típico

### Animar barra de vida al recibir daño

```
[On Message: Subject="take_damage"]
    → [BTCustomTask: new_hp = max(0, self.own.get('hp',100) - 10)]
    → [Play Tween: ID="hp_tween", Duration=0.5, Ease=ease_out]
    → [Set Property: prop="hp", Value={new_hp}]

[On Update] → [Tween Float:
                  ID = "hp_tween"
                  Property = "hp_visual"
                  From = {Get Property: prop="hp_visual"}
                  To = {Get Property: prop="hp"}]
              → [Set Bar: Value={hp_visual}]
```

### Volumen con fade

```
[On State Enter: FSM ID="game", State="quiet_zone"]
    └── On Enter ──► [Play Tween: ID="vol_fade", Duration=2.0, Ease=ease_in_out]

[On Update] → [Tween Float: ID="vol_fade", Property="vol_prop", From=1.0, To=0.2]
              → [Set Volume: Handle Key="bgm", Volume={vol_prop}]
```

## Notas

- La propiedad BGE se actualiza directamente cada frame que el tween está activo.
- El nodo no lee el valor actual de la propiedad como `From` — debes especificarlo explícitamente o conectarlo desde un socket de datos.
