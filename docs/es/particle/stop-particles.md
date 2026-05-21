# Stop Particles

**Tipo:** Acción  
**Categoría:** PARTICLE

Desactiva un emisor de partículas ocultándolo con `setVisible(False)`. Las partículas ya emitidas pueden continuar hasta expirar dependiendo de la configuración del sistema.

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
scene.objects['fx_smoke'].setVisible(False)
```

## Uso típico

### Detener humo al salir de zona de daño

```
[On State Exit: FSM ID="engine", State="running"]
    └── On Exit ──► [Stop Particles: Emitter Object="exhaust_fx"]
```

### Par Start / Stop

```
# Al entrar en estado de carga:
[On State Enter: FSM ID="weapon", State="charging"]
    └── On Enter ──► [Start Particles: Emitter Object="charge_fx"]

# Al salir del estado de carga:
[On State Exit: FSM ID="weapon", State="charging"]
    └── On Exit ──► [Stop Particles: Emitter Object="charge_fx"]
```

## Notas

- Ocultar el emisor no destruye las partículas ya en vuelo — depende del ajuste de vida del sistema de partículas.
- Para destruir completamente el emisor instanciado, usa `endObject()` (disponible en [BT Custom Task](../ai-bt/bt-custom-task.md)).
