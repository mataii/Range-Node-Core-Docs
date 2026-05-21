# Timer

**Tipo:** Dato  
**Categoría:** DATA

Devuelve el tiempo absoluto del sistema en segundos desde el inicio de la sesión de Range Game Engine. Equivalente a `Range.logic.getClockTime()`.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Time | Salida | Dato (Float) |

## Código generado

```python
Range.logic.getClockTime()
```

## Uso típico

### Calcular tiempo transcurrido entre eventos

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [BTCustomTask:
           self._bt_bb['last_jump_time'] = {Timer}]

# Más adelante:
[On Update] → [BT Condition: {Timer} - self._bt_bb.get('last_jump_time', 0) > 1.5]
                  └── True ──► [...]  # cooldown terminado
```

### Animación basada en tiempo absoluto

```
[On Update] → [Set Light Energy:
                   Energy = {Math: sin({Timer} * 2.0) * 0.5 + 1.0}]
```

## Notas

- `getClockTime()` es tiempo real en segundos — no se ve afectado por pausa de lógica de juego, pero sí por cambios en la escala de tiempo del sistema operativo.
- Para medir intervalos relativos (tiempo en un estado FSM), usa [Get State Time](../fsm/get-state-time.md).
- El valor es un float de alta precisión — dos llamadas en el mismo frame devuelven valores casi idénticos pero no necesariamente iguales.
