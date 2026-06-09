# Guías

La [documentación de referencia](../player/index.md) explica qué hace cada nodo. Estas guías explican cómo combinar nodos para construir sistemas de juego reales.

!!! tip "¿Nuevo en RNC?"
    Si todavía no has construido nada con RNC, comienza con [Primeros Pasos](../primeros-pasos.md) antes de continuar aquí.

---

## Contenido de las Guías

**[Sistemas de juego](sistemas/index.md)**  
Tutoriales paso a paso para sistemas de juego completos. Cada tutorial cubre dos enfoques: un enfoque monolítico para prototipos rápidos y un enfoque modular para proyectos de producción escalables.

**[Patrones de arquitectura](patrones/index.md)**  
Guías de decisión para elegir el enfoque correcto cuando ya conoces los nodos pero necesitas entender las ventajas y desventajas de cada arquitectura.

---

## Rutas de aprendizaje

### Ruta A — Prototipo funcionando hoy
[Primeros Pasos](../primeros-pasos.md) → [Prototipo FPS](../plantillas/prototipo-fps.md) → [Mi Primer FPS](sistemas/mi-primer-fps.md) (Método A)

### Ruta B — Aprender el sistema correctamente
[Primeros Pasos](../primeros-pasos.md) → [Mi Primer FPS](sistemas/mi-primer-fps.md) → [IA del enemigo](sistemas/ia-enemigo.md) → [Monolítico vs Modular](patrones/monolitico-vs-modular.md)

### Ruta C — Revisión de diseño para un proyecto existente
[Monolítico vs Modular](patrones/monolitico-vs-modular.md)

---

## Antes de comenzar

Estas guías asumen que has completado [Primeros Pasos](../primeros-pasos.md) y que estás familiarizado con:

- Crear un NodeTree y añadir nodos con `Shift + A`
- Registrar un componente: Info Componente → Add RNC Component → Assign NodeTree
- La diferencia entre nodos exec (tienen sockets `In`/`Out`) y nodos de datos (sin sockets exec)
