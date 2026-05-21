# Player Movement

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Aplica movimiento al objeto del jugador leyendo los ejes `_pi_move_x` / `_pi_move_y` publicados por **Player Input**. Soporta velocidad de sprint, suavizado de aceleración y control reducido en el aire.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Physics | Enum | `Character` | Modo de física: `Character` (applyMovement) o `Dynamic` (linearVelocity) |
| Speed | Float | `4.0` | Velocidad de caminar (unidades/s) |
| Sprint Speed | Float | `8.0` | Velocidad al hacer sprint (unidades/s) |
| Accel Lerp | Float (0.01–1.0) | `1.0` | Suavizado de aceleración: 1 = instantáneo, menor = más suave |
| Air Control | Float (0.0–1.0) | `0.3` | Multiplicador de velocidad mientras está en el aire |

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| Speed | Entrada | Data | Anula la propiedad Speed si está conectado |
| Sprint Speed | Entrada | Data | Anula la propiedad Sprint Speed si está conectado |
| Out | Salida | Exec | |

## Modos de física

### Character
Usa `applyMovement()` en espacio local con corrección de delta time:

```python
self.own.applyMovement((vx * dt, vy * dt, 0.0), True)
```

Ideal para objetos con física de tipo **Character** en Range.

### Dynamic
Asigna directamente `localLinearVelocity.x/y`, preservando la velocidad Z (gravedad):

```python
vel = self.own.localLinearVelocity.copy()
vel.x = vx
vel.y = vy
self.own.localLinearVelocity = vel
```

Ideal para objetos con física **Dynamic**.

## Cómo funciona el sprint

El nodo lee `_pi_sprint` (bool) publicado por Player Input. Si es `True`, usa `Sprint Speed` en lugar de `Speed`. El socket `Sprint Speed` permite anular el valor desde el grafo.

## Cómo funciona Accel Lerp

Se mantiene una velocidad interna (`_pm_vx`, `_pm_vy`) que hace lerp hacia la velocidad objetivo cada frame:

```
velocidad_actual = velocidad_actual + (objetivo - velocidad_actual) * accel_lerp
```

Con `Accel Lerp = 1.0` el movimiento es idéntico al original (instantáneo). Con valores menores el personaje acelera y desacelera gradualmente.

## Cómo funciona Air Control

Lee `_pj_in_air` de **Player Jump** (si está presente). Mientras el jugador esté en el aire, la velocidad objetivo se multiplica por `Air Control` antes del lerp, reduciendo la capacidad de maniobra aérea.

| Air Control | Efecto |
|-------------|--------|
| `1.0` | Control completo en el aire (sin restricción) |
| `0.3` | 30% del control normal en el aire (valor recomendado) |
| `0.0` | Sin control en el aire |

!!! warning "Requiere Player Input en el mismo objeto"
    Si Player Input no está presente, `_pi_move_x/y` y `_pi_sprint` no existen y el objeto no se moverá.

!!! info "Air Control requiere Player Jump"
    Si Player Jump no está en el grafo, `_pj_in_air` tendrá valor `False` por defecto, equivalente a Air Control = 1.0 siempre.

## Grafo de ejemplo

```
[OnUpdate] → [Player Input] → [Player Jump] → [Player Movement] → [Out]
```
