# Categoría FLOW

La categoría **FLOW** contiene nodos que controlan el *cuándo* y el *cuántas veces* de la ejecución, sin ser condiciones de valor. No evalúan datos externos — mantienen su propio estado interno para modificar el flujo de control.

## Diferencia con condiciones

| Tipo | Ejemplos | Lee datos externos |
|------|----------|--------------------|
| Condición | BTCondition, Distance Check | Sí (`ai_dist`, blackboard…) |
| FLOW | Do Once, Flip Flop, Gate, Delay | No — solo cuentan o miden tiempo |

Los nodos FLOW son **ciegos a los datos** — no importa quién les llegue, solo importa cuándo y cuántas veces.

## Nodos

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Do Once](do-once.md) | Branch (True/False) | Ejecuta True solo la primera vez; False en las siguientes |
| [Flip Flop](flip-flop.md) | Branch (True/False) | Alterna True/False en cada llamada |
| [Gate](gate.md) | Branch (Out/Skip) | Deja pasar cada N llamadas |
| [Delay](delay.md) | Branch (True/False) | Ejecuta True después de N segundos |

## Variables internas por nodo

Cada nodo usa variables de instancia nombradas con el nombre del nodo:

| Nodo | Variable(s) interna(s) |
|------|------------------------|
| Do Once | `self._do1_<nombre>` |
| Flip Flop | `self._ff_<nombre>` |
| Gate | `self._gate_<nombre>` |
| Delay | `self._dly_<nombre>`, `self._dly_<nombre>_done` |

Renombrar un nodo en el editor resetea su estado — el nombre del nodo determina las variables.

## Patrones comunes

### Inicialización única

```
[OnUpdate] → [Do Once]
                ├── True  ──► [BT Set Blackboard: initialized = True] → [setup logic]
                └── False ──► (nada — ya inicializado)
```

### Toggle de estado

```
[OnCollision] → [Flip Flop]
                    ├── True  ──► [BT Custom Task: open_door()]
                    └── False ──► [BT Custom Task: close_door()]
```

### Heartbeat cada 5 segundos

```
[OnUpdate] → [Delay: 5.0s, Repeat=True]
                 ├── True  ──► [BTCustomTask: update_minimap()]
                 └── False ──► (esperando)
```

### Ejecutar solo cada 3 frames activos

```
[OnUpdate] → [Gate: N=3]
                 ├── Out  ──► [lógica costosa]
                 └── Skip ──► (nada)
```
