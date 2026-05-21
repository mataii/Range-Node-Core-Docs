# Set Particle Visible

**Tipo:** Acción  
**Categoría:** PARTICLE

Muestra, oculta o alterna la visibilidad de un emisor de partículas sin reiniciar la simulación.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Emitter Object | String | `""` | Nombre del objeto emisor. Vacío = `self.own` |
| Mode | Enum | `TOGGLE` | `SHOW` = mostrar, `HIDE` = ocultar, `TOGGLE` = alternar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
# SHOW
scene.objects['fx_smoke'].setVisible(True)

# HIDE
scene.objects['fx_smoke'].setVisible(False)

# TOGGLE
_spv = scene.objects['fx_smoke']
_spv.setVisible(not _spv.visible)
```

## Diferencia con Start / Stop Particles

| Set Particle Visible | Start Particles |
|---------------------|-----------------|
| Solo cambia visibilidad | Cambia visibilidad + reinicia simulación |
| La simulación continúa corriendo en segundo plano | Reinicia desde frame 0 |
| Más ligero | Más completo para reactivar un efecto |

## Notas

- Usar `HIDE` no detiene el cómputo interno de la simulación — el emisor sigue avanzando aunque invisible.
- Para pausar y reiniciar limpiamente, usa [Stop Particles](stop-particles.md) + [Start Particles](start-particles.md).
