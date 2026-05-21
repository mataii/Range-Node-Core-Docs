# Enemy Combat

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 58 (después de Enemy Movement y Rotation)

Gestiona el ataque del enemigo. Publica `ai_in_attack_range` para que AI State Machine decida si entrar en ATTACK, y aplica daño al target cuando el cooldown lo permite.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Attack Range | Float (≥0.1) | `2.0` | Distancia máxima de ataque |
| Damage | Float | `10.0` | Daño por golpe |
| Cooldown | Float (≥0.01) | `1.0` | Segundos entre ataques |
| Attack Type | Enum | `Melee` | Melee o Ranged |

### Attack Type: Melee vs Ranged

| Tipo | Método de daño |
|------|----------------|
| Melee | Escribe directamente en `globalDict['_dmg_<target>']` |
| Ranged | También escribe en `globalDict['_dmg_<target>']` si no hay proyectil configurado |

> En la versión actual, ambos tipos usan el canal `_dmg_<target_name>`. El tipo Ranged se diferencia del Melee en el contexto de uso (animación, sonido), no en el mecanismo de daño.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_target_pos` | Enemy Perception |
| `ai_dist` | Enemy Perception |
| `ai_target` | Enemy Perception |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_in_attack_range` | bool | True si `ai_dist ≤ attack_range` y hay posición válida |

## Comportamiento

Cada frame:

1. Calcula si el target está en rango: `ai_in_attack_range = ai_dist ≤ attack_range`.
2. Si está en rango y el cooldown expiró:
   - Resetea el cooldown.
   - Escribe daño en `globalDict['_dmg_' + target.name]`.
3. Si no está en rango o hay cooldown: no ataca, pero actualiza `ai_in_attack_range`.

`ai_in_attack_range` es lo que AI State Machine usa para decidir entre CHASE y ATTACK.

## Canal de daño

```python
Range.logic.globalDict['_dmg_' + target.name] += damage
```

Si el target tiene **Damage Receiver**, recibirá el daño automáticamente. Si tiene **Player Health**, también (usa el mismo canal).

### Daño acumulado en el mismo frame

Si múltiples enemigos atacan al mismo target en el mismo frame, el daño se acumula:
```python
Range.logic.globalDict.get(key, 0.0) + damage
```

## Uso típico

### Pipeline completo

```
[Enemy Perception] → [AI State Machine] → [Enemy Combat] → [Enemy Animation]
```

AI State Machine pone `ai_attack_requested = True` cuando el estado es ATTACK. Enemy Combat aplica el daño según su propio cooldown.

### Ajuste de dificultad

```
Damage: 5.0
Cooldown: 2.0   # ataque lento
```

```
Damage: 25.0
Cooldown: 0.5   # ataque rápido
```

## Notas

- Enemy Combat **no decide** cuándo atacar — solo gestiona el cooldown y el canal de daño. La decisión de atacar la toma AI State Machine (estado ATTACK) y la señal `ai_in_attack_range`.
- El timer interno `_ec_time` no se resetea con el estado — un ataque puede ocurrir incluso si el estado acaba de cambiar a ATTACK, si el cooldown había expirado antes.
- Damage Receiver y Enemy Combat **no son compatibles** en el mismo objeto (el enemigo no puede atacarse a sí mismo, pero si el target es el mismo objeto, llegaría daño). Asegúrate de que `ai_target` sea un objeto distinto al propietario.
