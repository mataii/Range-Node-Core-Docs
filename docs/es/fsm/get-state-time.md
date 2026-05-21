# Get State Time

**Tipo:** Data  
**Categoría:** FSM

Nodo de datos que devuelve cuántos segundos lleva la FSM en su estado actual. El contador se reinicia automáticamente cada vez que `SetState` transiciona a un nuevo estado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| FSM ID | String | `"fsm"` | Identificador de la FSM a consultar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Seconds | Salida | Data (Float) |

## Código generado

```python
(Range.logic.getClockTime() - getattr(self, '_fsm_fsm_t', Range.logic.getClockTime()))
```

Usa `getClockTime()` (reloj de pared absoluto) — no se ve afectado por la velocidad de frames ni por `deltaTime`.

## Comportamiento

- Devuelve `0.0` antes de la primera llamada a `SetState` (porque `getattr` devuelve `getClockTime()` como fallback, y la resta es ≈ 0).
- El contador avanza en tiempo real mientras el objeto permanece en el mismo estado.
- Se reinicia a `0.0` cada vez que `SetState` transiciona a un estado distinto.
- **No** se reinicia si `SetState` se llama con el mismo estado (no-op).

## Uso típico

### Transición automática por tiempo

```
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Get State Time: FSM ID="door"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 2.0]
                                                       └── True ──► [Set State: State="open"]
```

### Cooldown de ataque

```
[On Update] → [On State: FSM ID="combat", State="idle"]
                  └── True ──► [Get State Time: FSM ID="combat"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 1.5]
                                                       └── True ──► [Set State: State="attacking"]
```

### Timeout de alerta

```
# Si lleva más de 10s en "alert" sin ver al jugador, volver a "patrol":
[On Update] → [On State: FSM ID="ai", State="alert"]
                  └── True ──► [Get State Time: FSM ID="ai"]
                                    ──(Seconds)──► [BT Condition: Seconds >= 10.0]
                                                       └── True ──► [Set State: State="patrol"]
```

### Barra de progreso / carga

```
[On Update] → [On State: FSM ID="loader", State="loading"]
                  └── True ──► [BTCustomTask:
                                    t = {Get State Time: FSM ID="loader"}
                                    progress = min(1.0, t / 5.0)  # carga en 5 segundos
                                    self.own['load_bar'] = progress]
```

### Animación por fases

```
[On Update] → [On State: FSM ID="boss", State="intro"]
                  └── True ──► [Get State Time: FSM ID="boss"]
                                    ──(Seconds)──► [BTCustomTask:
                                                        if Seconds < 2.0:
                                                            play_phase_1()
                                                        elif Seconds < 4.0:
                                                            play_phase_2()
                                                        else:
                                                            pass]
                                                   → [BT Condition: Seconds >= 5.0]
                                                       └── True ──► [Set State: State="combat"]
```

## Notas

- `Get State Time` usa `getClockTime()` — es tiempo real en segundos, no tiempo de juego escalado. Si el juego se pausa modificando la escala de tiempo de Range, este contador seguirá avanzando.
- El valor se recalcula en cada frame — no hay memoria intermedia, es siempre `now - start`.
- Para condiciones de timeout simples, es más limpio que mantener un timer manual con `deltaTime`.
- Si nunca se llamó a `SetState`, el valor es aproximadamente `0.0` (la resta de dos llamadas casi simultáneas a `getClockTime()`).
