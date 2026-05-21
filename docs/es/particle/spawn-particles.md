# Spawn Particles

**Tipo:** Acción  
**Categoría:** PARTICLE

Instancia un emisor de partículas en la escena usando `scene.addObject()`. Ideal para efectos de una sola vez como explosiones, impactos y salpicaduras.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Emitter | String | `"fx_explosion"` | Nombre del objeto emisor a instanciar (debe estar en capa inactiva) |
| Reference | String | `""` | Objeto de referencia para posición/orientación. Vacío = `self.own` |
| Lifetime | Int | `60` | Vida del objeto instanciado en frames (0 = infinito) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
scene.addObject('fx_explosion', self.own, 60)
```

Con objeto de referencia personalizado:

```python
scene.addObject('fx_explosion', scene.objects['hit_point'], 60)
```

## Uso típico

### Explosión al impacto

```
[On Collision: Property="explosive"] → [Spawn Particles:
                                            Emitter = "fx_explosion"
                                            Lifetime = 120]
```

### Efecto de impacto de bala

```
[Raycast: Hit] → [Spawn Particles:
                     Emitter = "fx_bullet_hit"
                     Reference = "hit_marker"
                     Lifetime = 30]
```

### Humo de inicio de motor

```
[On Start] → [Spawn Particles:
                 Emitter = "fx_engine_smoke"
                 Lifetime = 0]  # infinito
```

## Notas

- El objeto `Emitter` debe existir en una capa inactiva de la escena (no la capa activa), o la llamada fallará.
- `Lifetime = 0` crea el objeto sin tiempo límite — debe ser destruido manualmente con `endObject()`.
- `scene.addObject()` devuelve el objeto instanciado, pero este nodo no expone ese valor. Usa [BT Custom Task](../ai-bt/bt-custom-task.md) si necesitas una referencia al objeto spawneado.
