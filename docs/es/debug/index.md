# Categoría DEBUG

Nodos de diagnóstico y visualización en tiempo de ejecución. Útiles durante el desarrollo para inspeccionar valores, trazar trayectorias y verificar geometría sin necesidad de herramientas externas.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Print](print.md) | Acción | Imprime un mensaje en la consola |
| [Draw Line](draw-line.md) | Acción | Dibuja una línea en 3D durante un frame |
| [Draw Ray](draw-ray.md) | Acción | Dibuja un rayo a lo largo de un eje local del objeto |

## Notas

- Todos los nodos de esta categoría son solo para desarrollo. No tienen efecto sobre la lógica del juego.
- `Draw Line` y `Draw Ray` solo son visibles en el frame en que se ejecutan — deben llamarse cada frame (p. ej. desde `On Update`) para ver una línea continua.
- Las líneas dibujadas no aparecen en renders ni en capturas de pantalla del juego.
