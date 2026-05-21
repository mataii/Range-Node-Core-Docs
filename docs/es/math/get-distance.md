# Get Distance

**Tipo:** Dato  
**Categoría:** MATH

Devuelve la distancia entre dos objetos de la escena en unidades de mundo. Puede medir en XY (2D plano) o XYZ (3D completo).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| From | String | `""` | Objeto de origen. Vacío = `self.own` |
| To | String | `"target"` | Objeto de destino |
| Mode | Enum | `XYZ` | `XY` = distancia 2D plana, `XYZ` = distancia 3D |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Distance | Salida | Dato (Float) |

## Código generado

```python
# XYZ
(self.own.worldPosition - scene.objects['target'].worldPosition).length

# XY (ignora Z)
_gd_a = self.own.worldPosition
_gd_b = scene.objects['target'].worldPosition
((_gd_a[0]-_gd_b[0])**2 + (_gd_a[1]-_gd_b[1])**2)**0.5
```

## Uso típico

### Detectar proximidad al jugador

```
[On Update] → [BT Condition: {Get Distance: To="Player"} < 5.0]
                  └── True ──► [Set State: State="chase"]
```

### Distancia horizontal para IA de suelo

```
[On Update] → [BT Condition: {Get Distance: To="Player", Mode=XY} < 3.0]
                  └── True ──► [Set State: State="attack"]
```

## Notas

- Usa `worldPosition` — ignora la escala del objeto. Mide la distancia entre centros de objeto.
- El modo `XY` es útil para IA en plano horizontal donde la diferencia de altura no debe afectar la detección.
