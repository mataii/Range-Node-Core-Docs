# Damage Receiver

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 42 (después de Enemy Perception, antes de AI State Machine)

Gestiona los puntos de vida del objeto y lee daño desde el canal `_dmg_<nombre>` de `globalDict`. Publica el estado de HP en variables `ai_*` para el resto del sistema modular.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Max HP | Float (≥1.0) | `100.0` | Puntos de vida máximos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_hp` | float | HP actual |
| `self.ai_hp_max` | float | HP máximo |
| `self.ai_is_dead` | bool | True cuando HP ≤ 0 (se mantiene True) |
| `self.ai_took_damage` | bool | True solo el frame en que llegó daño |
| `self.ai_recent_damage` | bool | Alias de `ai_took_damage` |

## Canal de daño

Cualquier objeto puede dañar a este enemigo escribiendo en `globalDict`:

```python
Range.logic.globalDict['_dmg_' + enemy.name] = cantidad
```

El nodo lee y elimina (`pop`) este valor cada frame. Si no hay nada en el canal, el daño es 0.

### ¿Quién escribe en este canal?

| Fuente | Canal |
|--------|-------|
| Enemy Combat (melee/ranged) | `_dmg_<target_name>` |
| Weapon Hitscan (legacy) | `_dmg_<hit_name>` |
| Weapon Balancer | `_dmg_<target_name>` |
| Código personalizado | `Range.logic.globalDict['_dmg_<nombre>'] = dmg` |

## Comportamiento

Cada frame:

1. Lee `_dmg_<self.own.name>` de globalDict (y lo elimina).
2. Resta el daño a `_dr_hp`.
3. Actualiza `ai_hp`, `ai_took_damage`.
4. Si HP ≤ 0 y `ai_is_dead` era False: pone `ai_is_dead = True` (transición solo ocurre una vez).

`ai_is_dead` nunca vuelve a False — es un estado terminal. Para "revivir" al enemigo habría que destruir y reemplazar el objeto.

## Uso en el sistema modular

```
[Enemy Perception] → [Damage Receiver] → [AI State Machine]
```

AI State Machine lee `ai_hp`, `ai_hp_max` y `ai_is_dead` para decidir si entrar en RETREAT o DEAD.

### Hacer que un objeto dañe al enemigo

```python
# En cualquier BTCustomTask, script o nodo de otro objeto:
enemy = self.own.scene.objects.get('Enemy_01')
if enemy:
    Range.logic.globalDict['_dmg_' + enemy.name] = 25.0
```

### Daño del jugador (ej. Weapon Hitscan)

El nodo Weapon Hitscan del sistema PLAYER escribe automáticamente en `_dmg_<nombre>` del objeto golpeado. Si ese objeto tiene Damage Receiver, recibirá el daño.

## Diferencia con EnemyAI (prefab)

| Damage Receiver | EnemyAI (prefab) |
|----------------|-----------------|
| Canal: `_dmg_<nombre>` | Canal: `_ai_dmg_<nombre>` |
| Publica `ai_*` | No publica `ai_*` externas |
| Parte del sistema modular | Auto-contenido |

> **No mezclar** Damage Receiver con EnemyAI en el mismo objeto — usan canales de globalDict distintos.

## Notas

- `ai_took_damage` es True solo durante **un frame** — el frame en que llegó daño. El siguiente frame vuelve a False.
- Para reaccionar al daño (flash, sonido, stagger), conecta Enemy Hit Flash o lee `ai_took_damage` en Enemy Animation.
- Si quieres daño acumulado (varios proyectiles en el mismo frame), el canal suma correctamente porque `globalDict.get(key, 0) + dmg` acumula antes del `pop`.
