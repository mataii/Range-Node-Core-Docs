# Flee

**Tipo:** Exec (In → Out)  
**Categoría:** AI

Mueve al objeto propietario alejándose de un objetivo hasta alcanzar el radio seguro. Cuando el objetivo está más lejos que `Safe Radius`, el objeto decelera gradualmente.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | Enum | *(objetos de escena)* | Objeto del que huir |
| Speed | Float (≥0.01) | `4.0` | Velocidad de huida (unidades/s) |
| Safe Radius | Float (≥0.1) | `8.0` | Distancia a la que se considera "seguro" |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Suavizado de velocidad (1=instantáneo) |
| Physics | Enum | `Character` | Modo de física del objeto |

### Modos de física

| Modo | Método de movimiento |
|------|----------------------|
| `Character` | `applyMovement()` en espacio de mundo |
| `Dynamic` | Asigna `localLinearVelocity.x/y` (preserva Z) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

Cada frame:

1. Calcula `dirección = posición_propia - posición_objetivo` (dirección de alejamiento).
2. Ignora el eje Z (movimiento horizontal únicamente).
3. Si `distancia < safe_radius`: aplica velocidad en dirección de alejamiento.
4. Si `distancia ≥ safe_radius`: desacelera gradualmente (lerp hacia cero).

La velocidad se almacena en `self._fl_vel` y se interpola con `Vel Lerp` cada frame, produciendo aceleración y deceleración suaves.

## Uso típico

### Huida básica del jugador

```
Target: Player
Speed: 5.0
Safe Radius: 10.0
Vel Lerp: 0.1
```

```
[OnUpdate] → [Flee: Player]
```

### Flee condicional con Distance Check

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Flee: Player]    # huir si el jugador está cerca
    └── Far  ──► [Patrol]          # patrullar si está lejos
```

### Integración con AI State Machine

```
[AI State Machine]
    │ Out
    │
[Enemy Decision]
    ├── Chase ──► [Enemy Movement]    # perseguir
    └── Idle  ──► [Flee: SafeZone]   # alejarse de la zona de peligro
```

## Diferencia con Seek

| Seek | Flee |
|------|------|
| Se acerca al objetivo | Se aleja del objetivo |
| Para dentro del stop_radius | Para fuera del safe_radius |
| Tiene zona de frenado progresivo | Decelera cuando ya está seguro |

## Notas

- Si el Target no existe en escena, el nodo no mueve ni produce error.
- `self._fl_vel` es global para todos los nodos Flee del mismo componente — si hay dos nodos Flee en el mismo árbol, ambos compartirán la velocidad interna. Rename los nodos para distinguirlos.
- Useful combinado con **AI State Machine**: cuando el estado es RETREAT, conectar a Flee en lugar de a Enemy Movement.
