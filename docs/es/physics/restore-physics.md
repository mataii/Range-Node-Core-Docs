# Restore Physics

**Tipo:** Acción  
**Categoría:** PHYSICS

Reactiva la simulación de física del objeto después de haber sido suspendida con [Suspend Physics](suspend-physics.md).

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.restorePhysics()
```

## Uso típico

### Reanudar física al despausar el juego

```
[On Message: Subject="unpause"]
    → [Restore Physics]
```

### Activar física tras secuencia de animación

```
[On Message: Subject="spawn_done"]
    → [Restore Physics]
```

### Ciclo suspender / restaurar

```
[On Key Press: Key=P, Mode=PRESSED]
    → [Toggle: prop="physics_paused"]

[On Update] → [If Property: prop="physics_paused", Value=True]
                  └── True  ──► [Suspend Physics]
                  └── False ──► [Restore Physics]
```

## Notas

- No tiene efecto si la física no estaba suspendida.
- El objeto retoma la velocidad y el estado que tenía antes de la suspensión.
- Pareja natural de [Suspend Physics](suspend-physics.md).
