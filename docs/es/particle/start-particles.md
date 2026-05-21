# Start Particles

**Tipo:** Acción  
**Categoría:** PARTICLE

Activa un emisor de partículas existente en la escena: lo hace visible y reinicia la simulación con `reinstancePhysicsMesh()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Emitter Object | String | `""` | Nombre del objeto emisor en la escena. Vacío = `self.own` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_pe = scene.objects['fx_smoke']
_pe.setVisible(True)
_pe.reinstancePhysicsMesh()
```

## Uso típico

### Activar humo al entrar en zona de daño

```
[On Collision: Property="damage_zone"] → [Start Particles: Emitter Object="fx_smoke"]
```

### Iniciar partículas con FSM

```
[On State Enter: FSM ID="engine", State="running"]
    └── On Enter ──► [Start Particles: Emitter Object="exhaust_fx"]
```

## Notas

- `reinstancePhysicsMesh()` reinicia la simulación desde el inicio, lo que produce partículas desde el frame 0.
- Si el emisor ya estaba visible y activo, esta llamada reinicia la animación de partículas.
- Para un efecto de una sola vez sin necesidad de reiniciar, usa [Spawn Particles](spawn-particles.md).
