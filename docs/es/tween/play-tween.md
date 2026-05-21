# Play Tween

**Tipo:** Acción  
**Categoría:** TWEEN

Inicia una interpolación escribiendo sus parámetros en `globalDict`. Debe usarse junto con un nodo procesador como [Tween Position](tween-position.md), [Tween Float](tween-float.md) o [Tween Color](tween-color.md).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Tween ID | String | `"tween"` | Identificador único de esta interpolación |
| Duration | Float | `1.0` | Duración en segundos |
| Ease | Enum | `linear` | Función de easing: `linear`, `ease_in`, `ease_out`, `ease_in_out`, `bounce` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['_rnc_tw_door'] = {
    'dur': 1.0,
    'ease': 'ease_out',
    't': 0.0,
    'start': Range.logic.getClockTime()
}
```

## Uso típico

### Abrir puerta

```
[On Key Press: Key=E, Mode=PRESSED]
    → [Play Tween: ID="door", Duration=1.5, Ease=ease_out]

[On Update] → [Tween Position: ID="door", From=[0,0,0], To=[0,0,3]]
                  └── True ──► [Set State: State="open"]
```

### Fade de color al recibir daño

```
[On Message: Subject="take_damage"]
    → [Play Tween: ID="hit_flash", Duration=0.3, Ease=ease_out]

[On Update] → [Tween Color: ID="hit_flash", From=[2,0,0,1], To=[1,1,1,1]]
```

## Notas

- `Play Tween` reinicia la interpolación desde 0 si se llama mientras una ya está en curso con el mismo ID.
- El tiempo se mide con `getClockTime()` — es tiempo real, no afectado por la pausa de lógica del juego.
- Un ID único por cada interpolación simultánea — si dos objetos necesitan tweens independientes, usen IDs diferentes.
