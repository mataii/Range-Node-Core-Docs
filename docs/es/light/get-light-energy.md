# Get Light Energy

**Tipo:** Dato  
**Categoría:** LIGHT

Devuelve la intensidad actual de una luz de la escena como valor flotante.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Light Object | String | `""` | Nombre del objeto luz. Vacío = `self.own` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Energy | Salida | Dato (Float) |

## Código generado

```python
scene.objects['lamp_main'].energy
```

Expresión pura insertada donde se conecte el socket.

## Uso típico

### Condicional por energía actual

```
[On Update] → [BT Condition: {Get Light Energy: Light Object="lamp"} > 0.5]
                  └── True ──► [...]
```

### Ajuste proporcional

```
[On Update] → [BTCustomTask:
                   current = {Get Light Energy: Light Object="lamp_a"}
                   # reducir al 50% de la energía actual
              ] → [Set Light Energy: Light Object="lamp_b", Energy={current * 0.5}]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec.
- Lee `KX_LightObject.energy` directamente cada vez que se evalúa.
