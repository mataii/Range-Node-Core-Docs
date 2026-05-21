# Categoría LIGHT

Nodos para controlar luces en tiempo de ejecución. Permiten modificar intensidad, color, rango y visibilidad de objetos de luz de la escena.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Set Light Energy](set-light-energy.md) | Acción | Cambia la intensidad de una luz |
| [Set Light Color](set-light-color.md) | Acción | Cambia el color RGB de una luz |
| [Set Light Range](set-light-range.md) | Acción | Cambia el rango/distancia de influencia de una luz |
| [Toggle Light](toggle-light.md) | Acción | Activa, desactiva o alterna la visibilidad de una luz |
| [Get Light Energy](get-light-energy.md) | Dato | Devuelve la intensidad actual de una luz |

## Patrón de uso

Todos los nodos de luz apuntan a un objeto de la escena por nombre. Si el campo `Light Object` está vacío, usan `self.own` (el objeto que ejecuta el componente debe ser él mismo una luz).

```
[On Start] → [Set Light Energy: Light Object="lamp_main", Energy=2.5]
[On Update] → [Toggle Light: Light Object="lamp_flicker", Mode=TOGGLE]
```

## Notas

- Los atributos `energy`, `color` y `distance` corresponden a las propiedades de `KX_LightObject` de Range Game Engine.
- Cambiar el color acepta valores flotantes en rango [0.0, 1.0] por canal.
