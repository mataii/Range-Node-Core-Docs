# Suspend Physics

**Tipo:** Acción  
**Categoría:** PHYSICS

Suspende la simulación de física del objeto. El objeto deja de responder a fuerzas, gravedad y colisiones.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ghost | Bool | `False` | Si es `True`, el objeto queda como fantasma (detectable pero sin colisión sólida) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.suspendPhysics(False)
```

## Uso típico

### Congelar objeto al pausar el juego

```
[On Message: Subject="pause"]
    → [Suspend Physics]
```

### Suspender con modo fantasma (detectable pero sin colisión)

```
[On Message: Subject="go_ghost"]
    → [Suspend Physics: Ghost=True]
```

### Suspender al morir (para animar la muerte sin física)

```
[On Message: Subject="die"]
    → [Suspend Physics]
    → [Play Action: Action="death_anim"]
```

## Notas

- Usa [Restore Physics](restore-physics.md) para reactivar la simulación.
- Con `Ghost=True`, el objeto sigue reportando solapamientos pero no ejerce reacciones físicas.
- La posición del objeto no se actualiza por física durante la suspensión, pero puede moverse vía Transform o scripts.
