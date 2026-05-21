# Set Damping

**Tipo:** Acción  
**Categoría:** PHYSICS

Establece el amortiguamiento lineal y angular del objeto, controlando cuánto se desacelera por resistencia.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Linear | Float | `0.04` | Amortiguamiento lineal (0 = sin fricción, 1 = parada instantánea) |
| Angular | Float | `0.1` | Amortiguamiento angular (rotación) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.setDamping(0.04, 0.1)
```

## Uso típico

### Configurar fricción de personaje

```
[On Start] → [Set Damping: Linear=0.8, Angular=0.9]
```

### Objeto en el agua (alta resistencia)

```
[On Collision: Object="water"]
    → [Set Damping: Linear=0.6, Angular=0.6]
```

### Objeto en el aire (baja resistencia)

```
[On Collision: Object="air_zone"]
    → [Set Damping: Linear=0.01, Angular=0.01]
```

## Notas

- El amortiguamiento se aplica cada frame como una fracción de la velocidad actual.
- Valores de `0.0` eliminan por completo el frenado por resistencia del aire.
- Para simular superficies deslizantes, usa valores bajos de `Linear` junto con fuerzas de movimiento moderadas.
