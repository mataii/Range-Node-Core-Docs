# Set Light Energy

**Tipo:** Acción  
**Categoría:** LIGHT

Cambia la intensidad de una luz de la escena en tiempo de ejecución.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Light Object | String | `""` | Nombre del objeto luz. Vacío = `self.own` |
| Energy | Float | `1.0` | Nueva intensidad |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Energy | Entrada | Dato (Float) |

## Código generado

```python
scene.objects['lamp_main'].energy = 1.0
```

## Uso típico

### Encender una luz progresivamente

```
[On Update] → [BTCustomTask:
                   t = min(1.0, {Get State Time: FSM ID="light"} / 2.0)
              ] → [Set Light Energy: Light Object="lamp", Energy={t}]
```

### Pulso de luz (efecto parpadeo suave)

```
[On Update] → [Set Light Energy:
                   Light Object = "lamp_flicker"
                   Energy = {Math: sin(getClockTime() * 5) * 0.5 + 1.0}]
```

## Notas

- `energy` es la propiedad `KX_LightObject.energy` de Range Game Engine.
- Valores negativos están permitidos por la API pero producen resultados no definidos visualmente.
