# Categoría ANIMATION

Nodos para controlar animaciones de acción de Range Game Engine.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Play Action](play-action.md) | Acción | Reproduce una acción de animación |
| [Stop Action](stop-action.md) | Acción | Detiene la animación en una capa |

## Sistema de capas de animación

Range Game Engine soporta múltiples capas de animación simultáneas. Cada `Play Action` especifica una capa (0–7), lo que permite mezclar animaciones. Por ejemplo: capa 0 para locomoción, capa 1 para animaciones de brazos, capa 2 para expresiones faciales.

## Modos de reproducción

| Modo | Comportamiento |
|------|----------------|
| PLAY | Reproduce una vez de principio a fin |
| LOOP | Bucle continuo |
| PING_PONG | Ida y vuelta |
| PROPERTY | Controlado por propiedad BGE |
| FLIPPER | Sin bucle (igual que PLAY pero término diferente en BGE) |
| LOOPEND | Finaliza el bucle al llegar al final |
| LOOPSTOP | Detiene el bucle |
