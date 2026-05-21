# Camera Shake

**Tipo:** Procesador (ejecutar en la cámara)  
**Categoría:** CAMERA

Aplica el efecto de temblor de cámara cada frame. Lee los parámetros de `globalDict` (escritos por [Trigger Camera Shake](trigger-camera-shake.md)) y aplica desplazamientos aleatorios con fade al objeto cámara.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado (simplificado)

```python
_shk = Range.logic.globalDict.get('_rnc_shk', None)
if _shk and _shk['current'] < _shk['frames']:
    _shk['current'] += 1
    _shk_t = 1.0 - (_shk['current'] / _shk['frames'])  # fade 1→0
    _shk_i = _shk['intensity'] * _shk_t
    import random
    _shk_off = [random.uniform(-_shk_i, _shk_i) for _ in range(3)]
    _shk_off[2] = 0  # solo X e Y
    self.own.applyMovement(_shk_off, True)
```

## Dónde poner este nodo

Debe ejecutarse en el **objeto cámara**, no en el objeto que dispara el temblor:

```
# Árbol de nodos de la cámara:
[On Update] → [Camera Shake]
```

## Uso típico

### Configuración completa de temblor de cámara

```
# Objeto "Player" — dispara el temblor:
[On Message: Subject="take_damage"]
    → [Trigger Camera Shake: Intensity=0.3, Frames=20]

# Objeto "Camera" — procesa el temblor cada frame:
[On Update] → [Camera Shake]
```

## Notas

- El fade es lineal: el desplazamiento máximo ocurre en el primer frame y se reduce a 0 al final.
- El desplazamiento se aplica en espacio local de la cámara (`applyMovement(..., True)`).
- Si `globalDict['_rnc_shk']` no existe o el temblor ha terminado, el nodo no hace nada.
