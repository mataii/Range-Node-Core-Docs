# Set Bar

**Tipo:** Acción  
**Categoría:** UI

Escala un objeto en el eje X en proporción a un valor de 0.0 a 1.0. Diseñado para barras de progreso como vida, energía o experiencia.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Bar Object | Enum | — | Objeto que representa la barra |
| Max Scale X | Float | `1.0` | Escala local X cuando la barra está al 100% |
| Value | Float | `1.0` | Porcentaje de relleno (0.0 – 1.0, usado si el socket no está conectado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Value | Entrada | Dato (Float, 0.0 – 1.0) |
| Out | Salida | Exec |

## Código generado

```python
_bar = self.own.scene.objects.get('health_bar')
if _bar:
    _pct = max(0.0, min(1.0, float(0.75)))
    _sc = _bar.localScale
    _bar.localScale = [_pct * 1.0, _sc[1], _sc[2]]
```

## Uso típico

### Barra de vida

```
[On Message: Subject="take_damage"]
    → [Add To Property: prop="health", Value=-10]
    → [Set Bar:
           Bar Object=health_bar
           Value={Math: {Get Property: prop="health"} / 100.0}]
```

### Barra de carga de habilidad

```
[On Update]
    → [Set Bar:
           Bar Object=skill_bar
           Value={Math: {Get Property: prop="skill_charge"} / 50.0}]
```

## Notas

- Solo modifica la escala en X; Y y Z se mantienen intactos.
- El valor se clampa automáticamente entre `0.0` y `1.0`.
- `Max Scale X` debe coincidir con la escala original del objeto cuando representa el 100%.
- El objeto barra puede estar en la escena de juego o en una escena overlay de HUD.
