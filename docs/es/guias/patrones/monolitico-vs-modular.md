# Monolítico vs Modular

RNC ofrece dos formas de construir cualquier sistema de juego principal. Entender cuándo usar cada enfoque es una de las decisiones arquitectónicas más importantes en un proyecto de Range.

---

## Los dos enfoques

**Los nodos monolíticos** empaquetan un sistema completo en un único nodo. Lo configuras a través de propiedades y él gestiona todo internamente.

Ejemplos: `FPS Tools`, `Enemy AI`, `Vehicle Player`, `Drone AI`.

```
[On Update] → [FPS Tools: Camera=fps_camera, Walk Speed=5.0, Jump Force=7.0]
```

**Los pipelines modulares** dividen el mismo sistema en nodos individuales, cada uno responsable de un único comportamiento. Los nodos se comunican a través de variables de instancia `self.*` usando prefijos compartidos (`_pi_*`, `ai_*`, `weapon_*`, etc.).

Ejemplos: `Player Input + Player Movement + Player Jump + Mouse Look`, `Enemy Perception + AI State Machine + Enemy Decision + Seek + Enemy Combat`.

```
[On Update] → [Player Input] → [Player Movement] → [Player Jump]
[On Update] → [Mouse Look]
```

Ambos enfoques compilan al mismo modelo de ejecución `KX_PythonComponent` subyacente. La diferencia es puramente en cómo se organiza la lógica y qué puede cambiarse de forma independiente.

---

## Cuándo gana el enfoque monolítico

Usa un nodo monolítico cuando:

**Estás prototipando.** Los nodos monolíticos alcanzan un estado jugable en minutos. Valida el concepto del juego antes de comprometerte con una arquitectura. Un proyecto de game jam, una demo de presentación o una prueba de concepto encajan aquí.

**Los comportamientos por defecto se ajustan exactamente a tu diseño.** Si FPS Tools cubre todas las mecánicas de movimiento que necesitas, añadir complejidad arquitectónica no produce ningún beneficio. Los juegos simples se benefician de arquitecturas simples.

**El sistema está aislado.** Si el sistema no interactúa con lógica personalizada y no necesitará cambiar, un nodo monolítico es más fácil de mantener y razonar.

**Estás aprendiendo el sistema.** Construir con nodos monolíticos primero te muestra qué necesita hacer un pipeline completo. Cambiar a modular se vuelve más fácil cuando ya conoces el objetivo final.

---

## Cuándo gana el enfoque modular

Usa un pipeline modular cuando:

**Necesitas reemplazar un comportamiento.** ¿Quieres una curva de salto personalizada que se suavice en el punto más alto? Reemplaza Player Jump con un nodo personalizado — Player Input y Player Movement permanecen sin cambios.

**Necesitas insertar comportamiento entre pasos existentes.** Wall-run, agacharse, deslizamiento sobre hielo — estos encajan naturalmente como nodos entre Player Input y Player Movement. Los nodos monolíticos no tienen puntos de inserción.

**Tienes múltiples variantes del mismo sistema.** Un enemigo Pesado y un Scout pueden compartir Enemy Perception y AI State Machine mientras usan configuraciones Seek diferentes, cooldowns de Enemy Combat distintos y conjuntos de Enemy Animation separados. Con un nodo monolítico, cada variante requiere una configuración de propiedades separada sin reutilización estructural.

**Necesitas depurar comportamientos individuales.** En un pipeline modular, desconecta un nodo e inserta un Print para inspeccionar el estado intermedio `ai_*` o `_pi_*`. En un nodo monolítico, todo el estado es interno e invisible.

**Planeas añadir un Behavior Tree.** Los nodos BT se conectan al pipeline de IA modular en la posición de Enemy Decision. No pueden reemplazar un nodo monolítico Enemy AI.

**Estás construyendo para producción.** Los pipelines modulares sobreviven a los cambios de alcance. Añadir un coste de recurso de sprint, una barrera de stamina o un dash es un cambio estructural en un controlador monolítico, pero una simple inserción de nodo en uno modular.

---

## Criterios de decisión

| Si necesitas... | Usa |
|---|---|
| Tener algo funcionando hoy | Monolítico |
| Entregar en un game jam | Monolítico |
| Prototipar un concepto antes de conocer qué mecánicas necesita | Monolítico primero |
| Añadir una mecánica que el nodo no soporta como propiedad | Modular |
| Ajustar subsistemas individuales de forma independiente | Modular |
| Reutilizar partes en diferentes tipos de objetos | Modular |
| Integrar con Behavior Trees | Modular |
| Depurar un comportamiento específico de forma aislada | Modular |
| Construir un juego con alcance en evolución | Modular |

---

## ¿Puedes mezclar ambos enfoques?

Sí — y esta es con frecuencia la respuesta correcta para un juego en desarrollo activo.

Un patrón común: usar un nodo monolítico para el prototipado, luego reemplazarlo con un pipeline modular a medida que el diseño se clarifica. Debido a que ambos enfoques publican y leen el mismo protocolo de variables (`ai_*`, `_pi_*`, `weapon_*`), los componentes construidos para uno permanecen compatibles con otros que siguen el mismo protocolo.

**Ejemplo — escena mixta:** un juego mantiene `Enemy AI` para tipos de enemigos simples (guardias, torretas) y construye un pipeline modular completo solo para el jefe final que necesita un Behavior Tree personalizado y tres fases de movimiento distintas. Ambos coexisten en la misma escena sin conflictos porque se ejecutan en objetos separados.

**Qué evitar:** no ejecutes un nodo monolítico y sus equivalentes modulares en el **mismo objeto**. Ambos escriben en variables de instancia superpuestas y entrarán en conflicto en el mismo frame. Por ejemplo, ejecutar `FPS Tools` y `Player Movement` juntos creará un conflicto porque ambos intentan escribir `self.own.walkDirection` cada frame.

---

## Migrar de monolítico a modular

Cuando decides reemplazar un nodo monolítico con un pipeline modular:

1. **Lee la página de referencia** del nodo monolítico para entender qué propiedades y comportamientos encapsula. El tutorial de Sistemas de juego para ese sistema lista los nodos modulares equivalentes (ver [Mi Primer FPS — Método A vs B](../sistemas/mi-primer-fps.md#método-a-vs-método-b), [IA del enemigo — Método A vs B](../sistemas/ia-enemigo.md#método-a-vs-método-b)).

2. **Construye el pipeline modular en un objeto de prueba** antes de reemplazar el original. Confirma que el comportamiento observable — velocidad de movimiento, altura de salto, radio de detección, tiempo de ataque — es idéntico.

3. **Haz coincidir los valores de configuración.** Copia los valores de propiedades del nodo monolítico (Walk Speed, Jump Force, Detection Radius, etc.) a los nodos modulares correspondientes. El protocolo de variables garantiza compatibilidad de comportamiento cuando los valores coinciden.

4. **Registra un nuevo componente.** El NodeTree modular requiere su propio paso Assign NodeTree aunque viva en el mismo objeto que antes usaba el nodo monolítico.

5. **Elimina el componente antiguo** de Game Components solo después de confirmar que el pipeline modular funciona correctamente en modo play.

---

## Relacionado

- [Mi Primer FPS — Método A vs B](../sistemas/mi-primer-fps.md#método-a-vs-método-b)
- [IA del enemigo — Método A vs B](../sistemas/ia-enemigo.md#método-a-vs-método-b)
- Referencias de nodos: [FPS Tools](../../fps/fps-tools.md), [categoría PLAYER](../../player/index.md), [categoría AI](../../ai/index.md)
