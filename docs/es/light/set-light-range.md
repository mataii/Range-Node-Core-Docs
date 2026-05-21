# Set Light Range

**Tipo:** Acción  
**Categoría:** LIGHT

Cambia el rango de influencia (distancia) de una luz de la escena en tiempo de ejecución.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Light Object | String | `""` | Nombre del objeto luz. Vacío = `self.own` |
| Range | Float | `10.0` | Nueva distancia de influencia en unidades de mundo |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Range | Entrada | Dato (Float) |

## Código generado

```python
scene.objects['lamp_main'].distance = 10.0
```

## Uso típico

### Explosión que ilumina el entorno brevemente

```
[On State Enter: FSM ID="explosion", State="blast"]
    └── On Enter ──► [Set Light Energy: Light Object="blast_light", Energy=5.0]
                  ──► [Set Light Range: Light Object="blast_light", Range=20.0]

[On State Exit: FSM ID="explosion", State="blast"]
    └── On Exit ──► [Set Light Range: Light Object="blast_light", Range=0.0]
```

## Notas

- `distance` es la propiedad `KX_LightObject.distance` de Range Game Engine.
- Un valor de `0.0` elimina prácticamente toda influencia de la luz.
- El tipo de atenuación (lineal, cuadrática) lo controla el material de la luz en el editor, no este nodo.
