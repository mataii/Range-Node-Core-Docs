# Enemy Hit Flash

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 62 (después de Enemy Animation)

Aplica un efecto visual de destello de color al objeto cuando recibe daño. El color base se captura automáticamente y se restaura al expirar el efecto.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Duration | Float (≥0.01) | `0.15` | Duración del destello en segundos |
| Strength | Float (≥0.0) | `1.0` | Intensidad del efecto (suma a R, resta a G y B) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_took_damage` | Damage Receiver |

## Comportamiento

### Cada frame

1. Decrementa `_ehf_timer`.
2. Si `ai_took_damage = True`: establece `_ehf_timer = duration`.
3. Si `_ehf_timer ≤ 0`: captura el color base del objeto (`self.own.color`).
4. Calcula el color modificado:
   ```python
   color = [base[0] + strength, base[1] - strength, base[2] - strength, base[3]]
   color = [clamp(r, 0, 1), clamp(g, 0, 1), clamp(b, 0, 1), alpha]
   self.own.color = color
   ```

### Resultado visual

Con `Strength = 1.0`:
- R sube al máximo → el objeto se tiñe de rojo
- G y B bajan al mínimo → resultado es rojo puro

El efecto dura `duration` segundos y luego el color vuelve al base.

### Color base

El color base se captura **cuando el timer es 0** (cuando el destello no está activo). Si el material del objeto cambia de color durante el juego, el nodo captura el nuevo valor automáticamente.

## Uso típico

### Flash básico de impacto

```
Duration: 0.15
Strength: 1.0
```

```
[Damage Receiver] → ... → [Enemy Animation] → [Enemy Hit Flash]
```

### Destello suave para NPC

```
Duration: 0.3
Strength: 0.5
```

### Sin flash (desactivar)

Deja el nodo con `Strength = 0.0` o elimínalo del árbol.

## Requisitos

El objeto debe tener un material que responda a `self.own.color`. En Range Game Engine, los materiales EEVEE con Color Object habilitado reciben el valor. Los materiales con colores hardcodeados en el shader ignorarán el cambio.

## Notas

- `_ehf_base_color` se actualiza cada frame cuando no hay flash activo — si el objeto tiene una animación de color, el nodo la captura correctamente.
- Si `ai_took_damage` es True varios frames seguidos (daño acumulado), el timer se resetea en cada uno, extendiendo el efecto.
- Enemy Hit Flash opera solo en el color del objeto (`KX_GameObject.color`), no en los materiales directamente.
