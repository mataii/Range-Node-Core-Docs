# Set Light Color

**Tipo:** Acción  
**Categoría:** LIGHT

Cambia el color RGB de una luz de la escena en tiempo de ejecución.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Light Object | String | `""` | Nombre del objeto luz. Vacío = `self.own` |
| R | Float | `1.0` | Canal rojo [0.0 – 1.0] |
| G | Float | `1.0` | Canal verde [0.0 – 1.0] |
| B | Float | `1.0` | Canal azul [0.0 – 1.0] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| R | Entrada | Dato (Float) |
| G | Entrada | Dato (Float) |
| B | Entrada | Dato (Float) |

## Código generado

```python
scene.objects['lamp_main'].color = [1.0, 1.0, 1.0]
```

## Uso típico

### Luz de alerta roja

```
[On State Enter: FSM ID="alarm", State="alert"]
    └── On Enter ──► [Set Light Color: Light Object="alarm_lamp", R=1.0, G=0.0, B=0.0]
```

### Luz de ambiente según hora del día

```
[On Update] → [BTCustomTask:
                   h = self._bt_bb.get('hour', 12)
                   r = 1.0 if h > 18 else 0.8
                   g = 0.5 if h > 18 else 0.9
                   b = 0.2 if h > 18 else 1.0
              ] → [Set Light Color: R={r}, G={g}, B={b}]
```

## Notas

- `color` en `KX_LightObject` es una lista `[R, G, B]` de floats.
- Los valores se pasan directamente sin validación de rango — mantén los valores entre 0.0 y 1.0.
