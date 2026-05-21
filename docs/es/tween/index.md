# Categoría TWEEN

Nodos para interpolación suave de propiedades con funciones de easing. Permiten animar posición, valores flotantes y colores sin código de interpolación manual.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Play Tween](play-tween.md) | Acción | Inicia una interpolación (escribe parámetros en globalDict) |
| [Tween Position](tween-position.md) | Procesador/Rama | Interpola `worldPosition` de From a To |
| [Tween Float](tween-float.md) | Procesador/Rama | Interpola una propiedad BGE flotante |
| [Tween Color](tween-color.md) | Procesador/Rama | Interpola `self.own.color` RGBA |

## Funciones de easing disponibles

| Easing | Comportamiento |
|--------|----------------|
| `linear` | Velocidad constante |
| `ease_in` | Comienza lento, acelera |
| `ease_out` | Comienza rápido, desacelera |
| `ease_in_out` | Lento → rápido → lento |
| `bounce` | Rebote al final |

## Patrón de uso

`Play Tween` escribe los parámetros en `globalDict['_rnc_tw_<id>']`. El nodo `Tween*` correspondiente lee esos parámetros cada frame, calcula el valor interpolado y lo aplica. El nodo Tween bifurca en `True` (completado) / `False` (en curso).

```
[On State Enter] → [Play Tween: ID="door", Duration=1.0, Ease=ease_out]
[On Update] → [Tween Position: ID="door", From=[0,0,0], To=[0,0,2]]
                  └── True ──► [Set State: State="open"]
```
