# Categoría CURSOR

Nodos para controlar el cursor del ratón y capturar su movimiento para cámaras o personajes.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Set Cursor Visible](set-cursor-visible.md) | Acción | Muestra, oculta o alterna la visibilidad del cursor |
| [Center Cursor](center-cursor.md) | Acción | Mueve el cursor al centro de la pantalla |
| [Capture Mouse](capture-mouse.md) | Acción | Captura el movimiento del ratón cada frame (para mouselook) |
| [Get Mouse Delta](get-mouse-delta.md) | Dato | Devuelve el delta X o Y de la captura de ratón |
| [Get Cursor Pos](get-cursor-pos.md) | Dato | Devuelve la posición actual del cursor |
| [Is Cursor Visible](is-cursor-visible.md) | Dato | Devuelve si el cursor es visible |
| [Cursor Over](cursor-over.md) | Rama | Detecta si el cursor está sobre un objeto 3D |
| [Set Mouse Visible](set-mouse-visible.md) | Acción | Alias de Set Cursor Visible (módulo mouse_nodes) |
| [Set Mouse Position](set-mouse-position.md) | Acción | Mueve el cursor a una posición específica |

## Patrón de captura de ratón

Para mouselook, `Capture Mouse` calcula el delta por frame y lo almacena en atributos del objeto. Luego `Get Mouse Delta` recupera esos valores para aplicarlos a la rotación de cámara o personaje.

```
[On Update] → [Capture Mouse: Capture ID="cam", Sensitivity=0.3]
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="cam", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="cam", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)]
```
