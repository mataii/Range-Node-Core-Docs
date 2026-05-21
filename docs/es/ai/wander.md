# Wander

**Tipo:** Exec (In → Out)  
**Categoría:** AI

Mueve al objeto en una dirección aleatoria que cambia periódicamente. Produce movimiento orgánico y errático, ideal para comportamiento de merodeo sin destino fijo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Speed | Float (≥0.01) | `2.0` | Velocidad de deambulación (unidades/s) |
| Min Interval (s) | Float (≥0.1) | `1.0` | Mínimo de segundos antes de cambiar de dirección |
| Max Interval (s) | Float (≥0.1) | `3.0` | Máximo de segundos antes de cambiar de dirección |
| Dir Lerp | Float (0.005–1.0) | `0.05` | Suavizado de giro (menor = curvas más amplias) |
| Physics | Enum | `Character` | Modo de física del objeto |

### Modos de física

| Modo | Método |
|------|--------|
| `Character` | `applyMovement()` × `deltaTime()` |
| `Dynamic` | Asigna `localLinearVelocity.x/y` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

Cada frame:

1. Decrementa el timer `self._wd_timer`.
2. Si el timer llega a 0: elige un ángulo aleatorio entre -π y π, calcula un nuevo vector objetivo `self._wd_tgt`, y reinicia el timer con un valor aleatorio entre Min y Max Interval.
3. Interpola `self._wd_dir` hacia `self._wd_tgt` con `Dir Lerp` cada frame.
4. Aplica movimiento en la dirección interpolada.

Los módulos `random` y `math` se importan solo cuando cambia la dirección (en promedio cada 1–3 s), no cada frame.

## Estado persistente

| Variable | Descripción |
|----------|-------------|
| `self._wd_timer` | Segundos hasta el próximo cambio de dirección |
| `self._wd_dir` | Dirección actual (Vector3 normalizado) |
| `self._wd_tgt` | Dirección objetivo (hacia la que se interpola) |

## Efecto del Dir Lerp

| Dir Lerp | Comportamiento |
|----------|----------------|
| `0.005` | Giros muy lentos y amplios — trayectoria casi recta con curvas suaves |
| `0.05` | Giros moderados — merodeo natural (valor recomendado) |
| `0.3` | Giros rápidos — movimiento errático |
| `1.0` | Giro instantáneo — cambia de dirección abruptamente |

## Uso típico

### Merodeo básico

```
Speed: 2.0
Min Interval: 1.5
Max Interval: 4.0
Dir Lerp: 0.05
Physics: Character
```

```
[OnUpdate] → [Wander]
```

### Wander + Distance Check (acercarse si ve al jugador)

```
[Distance Check: Player, Threshold=12.0]
    ├── Near ──► [Seek: Player]     # perseguir
    └── Far  ──► [Wander]           # merodear
```

### Idle alternado con Wander

```
[BT Wait: 3.0s] → [Wander] → (loop)
```

## Notas

- El eje Z siempre es 0 en el movimiento — el objeto no vuela.
- El primer frame empieza con `_wd_dir = (0, 1, 0)` (dirección Y positiva). El primer cambio ocurre a los `random.uniform(min, max)` segundos.
- No hay detección de obstáculos incorporada. Si el objeto colisiona con geometría, seguirá intentando moverse en la misma dirección hasta que el timer cambie.
- Para añadir evasión de obstáculos, combina Wander con un raycast manual en BTCustomTask o usa EnemyAI que incluye obstacle avoidance.
