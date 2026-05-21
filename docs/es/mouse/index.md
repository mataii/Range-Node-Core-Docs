# Categoría MOUSE

Nodos para leer el estado de los botones del ratón, la rueda de desplazamiento y la posición del cursor. Complementan a la categoría [CURSOR](../cursor/index.md).

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Mouse Button](mouse-button.md) | Rama | Detecta click izquierdo, derecho o central |
| [Mouse Scroll](mouse-scroll.md) | Rama | Detecta desplazamiento de rueda hacia arriba o abajo |
| [Mouse Position](mouse-position.md) | Dato | Devuelve la posición X o Y del cursor |
| [Mouse Over](mouse-over.md) | Rama | Detecta si el cursor está sobre un objeto 3D |
| [Mouse Raycast](mouse-raycast.md) | Rama | Rayo desde la cámara al cursor con resultado de impacto |

## Diferencia entre Mouse Over y Cursor Over

Ambos nodos hacen un raycast desde la cámara al cursor, pero `Mouse Over` (en `mouse_nodes.py`) usa `getScreenRay` directamente y expone el objeto hit como resultado. `Cursor Over` (en `cursor_nodes.py`) usa un enfoque similar pero permite filtrar por propiedad BGE o nombre de objeto específico.
