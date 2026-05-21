# Random Chance

**Tipo:** Rama (Success / Fail)  
**Categoría:** MATH

Bifurca por probabilidad. Versión de rama de [Random Bool](random-bool.md) — combina la generación aleatoria con la bifurcación en un solo nodo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Probability | Float | `0.5` | Probabilidad de éxito [0.0 – 1.0] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Success | Salida | Exec (éxito — ocurre con P probabilidad) |
| Fail | Salida | Exec (fallo — ocurre con 1-P probabilidad) |

## Código generado

```python
if __import__('random').random() < 0.5:
    #SUCCESS_PATH#
else:
    #FAIL_PATH#
```

## Uso típico

### IA decide atacar o retirarse

```
[On State Enter: FSM ID="ai", State="decide"]
    └── On Enter ──► [Random Chance: Prob=0.7]
                         ├── Success ──► [Set State: State="attack"]
                         └── Fail ──► [Set State: State="retreat"]
```

### Drop de objeto al morir

```
[On Message: Subject="enemy_dead"] → [Random Chance: Prob=0.3]
                                         └── Success ──► [Add Object: Object="item_drop"]
```

## Notas

- Equivalente a `Branch` + `Random Bool`, pero en un nodo. Usa este cuando solo necesitas bifurcar por probabilidad sin reutilizar el valor booleano.
