# Draw Ray

**Tipo:** Acción  
**Categoría:** DEBUG

Dibuja un rayo a lo largo de un eje local del objeto durante el frame actual. Calcula la dirección usando la orientación de mundo del objeto y llama a `Range.render.drawLine()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Axis | Enum | `X` | Eje local: X, Y, Z, -X, -Y, -Z |
| Length | Float | `1.0` | Longitud del rayo en unidades de mundo |
| Color | Vector (RGB) | `[1,0,0]` | Color de la línea |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_dr_ori = self.own.worldOrientation
_dr_dir = _dr_ori.col[0]  # eje X local
_dr_start = list(self.own.worldPosition)
_dr_end   = [_dr_start[i] + _dr_dir[i] * 1.0 for i in range(3)]
Range.render.drawLine(_dr_start, _dr_end, [1,0,0])
```

El índice de columna cambia según el eje: X=0, Y=1, Z=2. Para ejes negativos se multiplica por -1.

## Uso típico

### Visualizar dirección de frente del objeto

```
[On Update] → [Draw Ray: Axis=Y, Length=2.0, Color=[0,1,0]]
```

### Depurar orientación de arma

```
[On Update] → [Draw Ray: Axis=Z, Length=5.0, Color=[1,0,0]]
```

### Comprobar eje de ataque de IA

```
[On Update] → [Draw Ray: Axis=-Y, Length=3.0, Color=[1,0.5,0]]
```

## Diferencia con Draw Line

| Draw Ray | Draw Line |
|----------|-----------|
| Siempre parte de `self.own.worldPosition` | Puntos de inicio/fin configurables |
| Dirección relativa al eje local del objeto | Coordenadas absolutas de mundo |
| Rota con el objeto automáticamente | Puntos fijos o vinculados a otros objetos |

## Notas

- Solo visible durante el frame de ejecución. Úsalo desde `On Update` para verlo continuamente.
- `worldOrientation.col[i]` devuelve el vector columna de la matriz de rotación, que corresponde al eje local del objeto en espacio de mundo.
