# BT Inverter

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT

Decorador que invierte el resultado del nodo anterior. `SUCCESS` se convierte en `FAILURE` y viceversa. `RUNNING` pasa sin cambio.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Tabla de transformación

| `_bt_last_result` entrada | `_bt_last_result` salida |
|---------------------------|--------------------------|
| `'SUCCESS'` | `'FAILURE'` |
| `'FAILURE'` | `'SUCCESS'` |
| `'RUNNING'` | `'RUNNING'` (sin cambio) |

## Uso típico

Convierte una condición positiva en negativa sin cambiar el nodo que la genera:

```
# "Atacar si NO está en cooldown"
[BT Condition: in_cooldown] → [BT Inverter] → [BT Selector]
                                                    ├── Success ──► [Atacar]
                                                    └── Failure ──► [Esperar]
```

Sin Inverter necesitarías invertir la lógica en el BTCondition. Con Inverter, reutilizas el mismo BTCondition para múltiples propósitos.

## Otro uso: convertir fallo en éxito para Sequence

```
# "Continuar la secuencia aunque falle el intento de abrir la puerta"
[BT Custom Task: try_open_door] → [BT Inverter]
# Si open_door falla (FAILURE) → Inverter da SUCCESS → Sequence continúa
```

## Posición en el grafo

Debe ir **inmediatamente después** del nodo cuyo resultado quieres invertir y **antes** del compuesto que lo lee:

```
✓  [BTCondition] → [BTInverter] → [BTSelector]
✗  [BTInverter] → [BTCondition] → [BTSelector]
```

## Notas

- No tiene propiedades configurables.
- No afecta el blackboard.
- Múltiples inversores en cadena son válidos (dos inversores = sin efecto).
