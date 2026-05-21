# Categoría GAMEPAD

Nodos para leer entrada de mandos/joysticks conectados al sistema. Soportan múltiples dispositivos mediante índice.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Gamepad Connected](gamepad-connected.md) | Rama | Detecta si un gamepad está conectado |
| [Gamepad Button](gamepad-button.md) | Rama | Detecta pulsación, mantenimiento o liberación de un botón |
| [Gamepad Axis](gamepad-axis.md) | Dato | Devuelve el valor de un eje analógico con zona muerta |
| [Gamepad Any Button](gamepad-any-button.md) | Rama | Detecta si cualquier botón está pulsado |

## Índice de dispositivo

El parámetro `Gamepad Index` (0–7) selecciona el dispositivo. `Range.logic.joysticks[idx]` devuelve `None` si no hay ninguno conectado en ese slot. Usa siempre `Gamepad Connected` antes de leer ejes o botones en escenas donde el gamepad es opcional.

## Notas

- Los índices de botones y ejes dependen del driver del sistema operativo y del modelo de mando.
- El estado de "pulsado" y "liberado" se calcula mediante un flag previo almacenado en `Range.logic.globalDict['_gp<idx>_prev']`.
