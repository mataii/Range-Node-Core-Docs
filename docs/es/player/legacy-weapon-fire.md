# Weapon Fire (Legacy)

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

> **Deprecado.** Este nodo pertenece al pipeline de armas original. Para proyectos nuevos usa **Weapon Fire Executor** junto con Weapon State Publisher, Weapon Reload y Weapon Balancer.

Controla el disparo de arma en modo Semi-Auto o Auto. En Semi, delega en Player Input (`_pi_fire`). En Auto, gestiona un temporizador de cadencia propio y sintetiza pulsos de disparo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Mode | Enum | `Semi-Auto` | `Semi-Auto` (un disparo por pulsación) o `Auto` (fuego continuo mientras se mantiene) |
| Fire Rate | Float (0.1–100) | `10.0` | Disparos por segundo (solo en modo Auto) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

**Semi-Auto:** El nodo compila a `pass`. El disparo depende enteramente del pulso `_pi_fire` de Player Input (`.activated` del botón izquierdo del ratón).

**Auto:** El nodo gestiona `_wf_cd` (cooldown en segundos). Mientras el botón izquierdo esté pulsado (`.active`) y el cooldown sea ≤ 0, fuerza `self._pi_fire = True` y reinicia el cooldown a `1 / Fire Rate`. Si no se cumple la condición, fuerza `self._pi_fire = False`.

## Posición en el grafo

```
[Player Input] → [Weapon Fire] → [Inventory Manager] → ...
```

## Migración al sistema nuevo

| Legacy | Nuevo sistema |
|--------|---------------|
| Weapon Fire (Semi) | Weapon Fire Executor con `Mode = Semi` |
| Weapon Fire (Auto) | Weapon Fire Executor con `Mode = Auto` + `Fire Rate` |
| — | Weapon Fire Executor con `Mode = Burst` (nuevo) |
