# Get Velocity

**Tipo:** Dato  
**Categoría:** PHYSICS

Devuelve un componente de la velocidad lineal del objeto. Permite obtener X, Y, Z individuales o la velocidad de magnitud en plano XY o XYZ.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Component | Enum | `X` | `X`, `Y`, `Z`, `Speed XY` (magnitud horizontal), `Speed XYZ` (magnitud 3D) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
# Component = X
self.own.linearVelocity[0]

# Component = Speed XY
(_v := self.own.linearVelocity, (_v[0]**2 + _v[1]**2)**0.5)[-1]

# Component = Speed XYZ
self.own.linearVelocity.length
```

## Uso típico

### Mostrar velocímetro

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Get Velocity: Component=Speed XY}
                              Suffix = " m/s"
                              Decimals = 1}]
```

### Detectar si el objeto está en movimiento

```
[On Update] → [BT Condition: {Get Velocity: Component=Speed XYZ} > 0.5]
                  └── True ──► [Play Action: Action="run_anim"]
```

### Condicionar salto por velocidad vertical

```
[On Update] → [BT Condition: {Get Velocity: Component=Z} > -0.1]
                  └── True ──► [...]  # en suelo o subiendo
```

## Notas

- `linearVelocity` es el vector de velocidad en unidades de mundo por segundo.
- `Speed XY` ignora el componente vertical — útil para sistemas de movimiento horizontal.
