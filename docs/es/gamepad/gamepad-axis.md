# Gamepad Axis

**Tipo:** Dato  
**Categoría:** GAMEPAD

Devuelve el valor de un eje analógico del gamepad aplicando zona muerta. El valor resultante está en el rango [-1.0, 1.0].

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Gamepad Index | Int | `0` | Slot del dispositivo (0–7) |
| Axis Index | Int | `0` | Índice del eje según el driver del SO |
| Deadzone | Float | `0.1` | Valor mínimo absoluto para considerar movimiento real |
| Invert | Bool | `False` | Invierte el signo del valor |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
(_gp0_ax := Range.logic.joysticks[0].axisValues[0] if Range.logic.joysticks[0] else 0.0,
 _gp0_ax if abs(_gp0_ax) > 0.1 else 0.0)[-1]
```

Con `invert=True` se multiplica el resultado por `-1`.

## Uso típico

### Mover personaje con stick izquierdo

```
[On Update] → [BTCustomTask:
                   move_x = {Gamepad Axis: Index=0, Axis=0}
                   move_y = {Gamepad Axis: Index=0, Axis=1}
                   self.own.applyMovement([move_x, move_y, 0], True)]
```

### Rotar cámara con stick derecho

```
[On Update] → [BTCustomTask:
                   rot_x = {Gamepad Axis: Index=0, Axis=2}
                   rot_y = {Gamepad Axis: Index=0, Axis=3, Invert=True}
                   # aplicar rotación a cámara...]
```

## Notas

- Usa el operador walrus (`:=`) para evitar evaluar `joysticks[idx]` dos veces.
- Si el gamepad no está conectado, devuelve `0.0`.
- Los índices de eje varían por modelo de mando y driver. El eje 0 suele ser X del stick izquierdo, 1 es Y del stick izquierdo, 2 es X del stick derecho, 3 es Y del stick derecho.
