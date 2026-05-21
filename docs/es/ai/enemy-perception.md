# Enemy Perception

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 40 (primero del sistema modular)

Detecta al objetivo usando distancia, ángulo de campo visual (FOV) y raycast. Publica los resultados en variables `ai_*` que el resto del sistema modular lee.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | Enum | *(objetos de escena)* | Objeto a detectar |
| View Distance | Float (≥0.1) | `12.0` | Rango máximo de detección |
| Aggro Radius | Float (≥0.0) | `2.5` | Radio de detección automática sin FOV ni raycast |
| Use FOV | Bool | `True` | Limitar detección a un cono frontal |
| FOV Angle | Float (1–360) | `90.0` | Apertura total del cono en grados |
| Use Raycast | Bool | `True` | Requerir línea de visión sin obstáculos |
| Raycast Prop | String | `""` | Propiedad BGE que filtra obstáculos del raycast |
| Memory Duration | Float (≥0.0) | `3.0` | Segundos que recuerda al objetivo tras perderlo (0 = olvido inmediato) |
| Perception Interval | Float (≥0.0) | `0.0` | Segundos entre comprobaciones (0 = cada frame) |
| Debug | Bool | `False` | Imprimir estado por consola |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_can_see` | bool | True si el target es visible este frame |
| `self.ai_has_line_of_sight` | bool | True si el raycast llega al target sin obstáculos |
| `self.ai_dist` | float | Distancia XY al target (9999 si no existe) |
| `self.ai_target` | KX_GameObject\|None | Referencia al objeto target |
| `self.ai_target_pos` | Vector\|None | Posición del target si visible; None si oculto |
| `self.ai_last_seen_pos` | Vector\|None | Última posición conocida (persiste después de perder vista) |

## Comportamiento

### Lógica de detección (cada comprobación)

```
Si distancia ≤ aggro_radius:
    → detección inmediata (sin FOV, sin raycast)
Si distancia ≤ view_distance:
    Si use_fov:
        → calcular dot product con eje frontal Y del enemigo
        → si dot ≥ cos(fov_angle/2): continuar
    Si use_raycast:
        → rayCast al target
        → detección solo si hit == target
    Si no use_raycast:
        → detección por distancia + FOV
```

### Perception Interval

Si `Perception Interval > 0`, la comprobación completa solo se ejecuta cada N segundos. Esto ahorra CPU en niveles con muchos enemigos. La publicación de variables (`ai_can_see`, etc.) solo se actualiza en esos frames.

### Memoria

Cuando el target se pierde de vista, `ai_last_seen_pos` conserva la última posición conocida durante `memory_duration` segundos. Transcurrido ese tiempo, `ai_target` se pone a None.

`ai_can_see = False` no borra `ai_last_seen_pos` — esto permite que AI State Machine envíe al enemigo a buscar en ese punto.

## Uso en el sistema modular

Enemy Perception debe ejecutarse **antes** de AI State Machine. El `execution_order = 40` garantiza que sus variables estén actualizadas cuando el SM las lee:

```
[OnUpdate] → [Enemy Perception] → [Damage Receiver] → [AI State Machine] → ...
```

## Ejemplo de configuración

### Guardia con cono frontal y memoria de 5 segundos

```
Target: Player
View Distance: 15.0
Aggro Radius: 2.0
Use FOV: True
FOV Angle: 120.0
Use Raycast: True
Memory Duration: 5.0
Perception Interval: 0.1  (actualiza cada 100ms)
```

### Enemigo omnisciente sin FOV

```
Use FOV: False
Use Raycast: False
View Distance: 20.0
```

## Notas

- La distancia para FOV y memoria se calcula en el plano XY — la altura no afecta la detección.
- El coseno del ángulo de FOV se calcula en compilación, no en runtime, para ahorrar CPU.
- Si el target se destruye durante el juego, todas las variables `ai_*` se resetean a sus valores por defecto sin errores.
- El modo `debug` imprime `see`, `dist`, y `los` por consola cada frame — útil para ajustar parámetros.
