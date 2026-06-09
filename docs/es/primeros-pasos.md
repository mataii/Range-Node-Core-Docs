# Primeros Pasos

Esta guía te lleva desde cero hasta tu primer objeto con lógica visual
funcionando en Range Game Engine. Construirás un cubo que rota continuamente,
sin escribir una sola línea de código Python.

!!! info "Addon en desarrollo activo"
    Range Node Core está en desarrollo activo. Algunos flujos de trabajo,
    nombres de botones o pasos de configuración pueden cambiar en versiones
    futuras. Esta guía refleja el comportamiento de la versión actual.

---

## Lo que vas a construir

Un cubo que gira sobre su eje Z cada frame, usando solo dos nodos:

| Nodo | Categoría | Función |
|------|-----------|---------|
| **On Update** | EVENT | Punto de entrada — se ejecuta cada frame |
| **Transform** | OBJECT | Aplica la rotación al objeto |

---

## Requisitos previos

- Blender 2.79 con Range Game Engine instalado
- El addon **Range Node Core** activado
- Una escena con al menos un objeto mesh (el cubo por defecto sirve)

---

## Paso 1 — Activar el addon

Ve a **Archivo → Preferencias del usuario → Addons**, busca
**Range Node Core** y actívalo.

Si ya está activo, puedes continuar en el paso 2.

<!-- captura: panel de Addons con Range Node Core activado y el checkbox marcado -->

---

## Paso 2 — Abrir el Editor de Nodos

Cambia uno de los editores a **Node Editor**.

!!! warning "Selecciona el tipo correcto"
    La cabecera del Node Editor tiene un selector de tipo que por defecto
    muestra *Shader Nodes* o *Compositor*. Cámbialo a **Range Node Core**.
    Si esta opción no aparece, el addon no está activado correctamente.

<!-- captura: cabecera del Node Editor con el selector mostrando "Range Node Core" -->

---

## Paso 3 — Seleccionar el objeto

En el viewport 3D, selecciona el objeto al que quieres agregar lógica.
Para esta guía usaremos el cubo por defecto.

El Node Editor mostrará el NodeTree vinculado al objeto seleccionado,
o quedará vacío si aún no tiene ninguno.

---

## Paso 4 — Crear un NodeTree

Con el objeto seleccionado, haz clic en el botón **Nuevo** en la cabecera
del Node Editor.

Esto crea un NodeTree vacío y lo asocia a los datos del objeto activo.
El NodeTree almacena el grafo visual de nodos, pero por sí solo no ejecuta
nada durante el juego — para eso necesitas registrarlo como componente,
lo cual haremos más adelante.

Puedes renombrar el NodeTree haciendo doble clic en el campo del nombre
en la cabecera.

<!-- captura: botón "Nuevo" en la cabecera del Node Editor, y el lienzo
     vacío después de crearlo, con el nombre del NodeTree visible -->

---

## Paso 5 — Añadir el nodo On Update

Presiona `Shift + A` para abrir el menú de nodos y navega a
**EVENT → On Update**. Haz clic para colocarlo en el lienzo.

**On Update** es el punto de entrada del árbol. Su socket `Exec` de salida
dispara la lógica conectada cada frame, mientras el objeto exista en la escena.

<!-- captura: nodo On Update en el lienzo, mostrando su socket Exec de salida -->

---

## Paso 6 — Añadir el nodo Transform

Presiona `Shift + A` nuevamente y navega a **OBJECT → Transform**.
Colócalo a la derecha del nodo On Update.

---

## Paso 7 — Configurar el nodo Transform

Selecciona el nodo Transform y ajusta sus propiedades:

| Propiedad | Valor |
|-----------|-------|
| Mode | `ROTATE` |
| X | `0.0` |
| Y | `0.0` |
| Z | `0.3` |
| Local | `True` |

!!! note "Radianes, no grados"
    El valor `Z = 0.3` está en **radianes por frame**. A 60 FPS esto equivale
    aproximadamente a una vuelta completa cada 3.5 segundos.  
    Fórmula de conversión: `grados × π ÷ 180`  
    Ejemplo: 5° por frame → `5 × 3.14159 ÷ 180 ≈ 0.087`

<!-- captura: nodo Transform con Mode=ROTATE y Z=0.3 configurados -->

---

## Paso 8 — Conectar los nodos

Arrastra desde el socket **Exec** (salida) del nodo **On Update** hasta el
socket **In** (entrada) del nodo **Transform**.

El grafo debe quedar así:

```
[On Update] ──Exec──► [Transform: ROTATE Z=0.3]
```

<!-- captura: ambos nodos conectados con el cable Exec visible entre ellos -->

---

## Registrar el componente

El NodeTree que acabas de construir almacena la lógica visual, pero aún
no está conectado al sistema de ejecución del juego. Para que Range lo
ejecute en tiempo de juego, el NodeTree debe estar vinculado a un
**Game Component** en el objeto.

El componente que RNC registra es:

```
range_node_core.runtime.rnc_component.RNC_Component
```

Este identificador le dice a Range qué clase Python debe instanciar como
componente del objeto. Normalmente no necesitas escribirlo: el botón
**Info Componente** lo copia al portapapeles automáticamente.

Este proceso tiene dos partes: crear el componente con la identificación
correcta, y luego asignar el NodeTree explícitamente.

!!! note "¿Por qué este proceso es manual?"
    Cuando ejecutas el juego, Range necesita saber que debe instanciar
    `range_node_core.runtime.rnc_component.RNC_Component` como componente
    de este objeto. El sistema de Game Components de Range requiere que ese
    registro exista antes de iniciar la simulación.

    El proceso es manual porque Range no expone una API pública que permita
    registrar un `KX_PythonComponent` desde Python en tiempo de edición.
    El botón **Info Componente** existe como solución práctica: genera la
    cadena de registro correcta para que puedas pegarla sin conocer la ruta
    interna del módulo. En versiones futuras del addon, este paso podría
    automatizarse si Range incorpora esa API.

### 1. Copiar la información del componente

Tienes dos formas de acceder al botón **Info Componente**:

**Opción A — Desde el panel lateral del Node Editor:**

1. Presiona `N` para abrir el panel lateral
2. En la pestaña **RNC**, haz clic en **Info Componente**

**Opción B — Desde el nodo On Update:**

1. Selecciona el nodo On Update en el lienzo
2. En el panel de propiedades del nodo, haz clic en **Info Componente**

Ambas opciones copian al portapapeles el identificador
`range_node_core.runtime.rnc_component.RNC_Component`, que es el componente
Python que RNC usa para ejecutar el NodeTree. No necesitas escribirlo
manualmente; solo pégalo en el siguiente paso.

<!-- captura: panel lateral con pestaña RNC y botón "Info Componente" visible -->

### 2. Ir a las Game Properties del objeto

Cambia al **Properties Editor** y selecciona la pestaña **Game**
(ícono de controlador de juego).

Localiza la sección **Game Components** dentro de ese panel.

<!-- captura: Properties Editor con la pestaña Game activa,
     sección Game Components visible -->

### 3. Eliminar el componente "module" si existe

Si la lista de Game Components contiene un elemento llamado `module`,
selecciónalo y elimínalo antes de continuar.

!!! note "¿Por qué existe ese componente?"
    Range a veces agrega un componente placeholder llamado `module`
    automáticamente. Si se deja, puede interferir con el componente
    de RNC que vas a crear a continuación.

<!-- captura: componente "module" seleccionado en la lista,
     con el botón de eliminar visible -->

### 4. Agregar el componente RNC

Haz clic en el botón **Add RNC Component** en la sección Game Components.

Aparecerá una ventana emergente con un campo de texto. Pega ahí el texto
que copiaste (`Ctrl + V` en Windows/Linux, `Cmd + V` en Mac). El campo
debe quedar así:

```
range_node_core.runtime.rnc_component.RNC_Component
```

Haz clic en **OK** para confirmar.

<!-- captura: ventana emergente con la cadena pegada en el campo,
     antes de confirmar con OK -->

### 5. Asignar el NodeTree

Después de confirmar, el nuevo componente aparece en la lista de Game
Components, pero aún no sabe qué NodeTree debe ejecutar.

Haz clic en **Assign NodeTree**.

Esto vincula el componente con el NodeTree que tienes abierto actualmente
en el Node Editor. A partir de este momento, cada vez que ejecutes el
juego, Range compilará ese grafo de nodos y lo ejecutará como parte del
componente.

!!! warning "No omitas este paso"
    Si no asignas el NodeTree, el componente no tiene lógica que ejecutar
    y el cubo no rotará. No habrá ningún mensaje de error: simplemente
    no ocurrirá nada.

<!-- captura: botón "Assign NodeTree" visible en la entrada del componente
     dentro de Game Components -->

<!-- captura compuesta sugerida: secuencia de cuatro imágenes en fila que
     muestran el flujo completo de registro:
     (1) botón "Info Componente" en el panel lateral,
     (2) ventana emergente con range_node_core.runtime.rnc_component.RNC_Component pegado,
     (3) componente recién creado en la lista de Game Components,
     (4) estado final con "Assign NodeTree" ya presionado y el nombre
         del NodeTree visible en el panel del componente -->

---

## Paso 9 — Ejecutar el juego

Coloca el cursor sobre el viewport 3D y presiona **P** (o usa el botón
**Start** del Game Engine si está visible en la cabecera).

**Resultado esperado:** el cubo comienza a girar continuamente sobre su eje Z.

Presiona **Escape** para detener la simulación.

<!-- captura: cubo girando durante la ejecución del Game Engine -->

---

## Qué aprendiste

Con estos pasos completaste el flujo fundamental de RNC:

1. **Construir lógica visualmente** — nodos conectados en el Node Editor
2. **Registrar el componente** — copiar la info, agregar el componente
   y asignar el NodeTree vincula el grafo con el sistema de ejecución
3. **Ejecutar** — Range compila el grafo y lo ejecuta cada frame

Este mismo flujo aplica para cualquier lógica, desde movimiento del jugador
hasta sistemas complejos de IA.

---

## Siguientes pasos

- [On Update](event/on-update.md) — referencia completa del nodo de entrada
- [Transform](object/transform.md) — referencia completa del nodo de transformación
- [On Key Press](event/on-key-press.md) — responder al input del jugador
- [Branch](logic/branch.md) — lógica condicional

---

## Preguntas frecuentes

**¿Tengo que repetir el proceso de registro cada vez que modifico el grafo?**

No. Una vez que el componente está registrado y el NodeTree asignado,
cualquier cambio que hagas en los nodos se compila automáticamente la
próxima vez que ejecutes el juego. Solo necesitas repetir el proceso si
cambias el nombre del NodeTree o creas uno nuevo.

**El cubo no gira. ¿Qué puede haber salido mal?**

Verifica en este orden:

1. ¿El socket Exec de On Update está conectado al socket In de Transform?
2. ¿Hiciste clic en **Assign NodeTree** después de agregar el componente?
3. ¿Eliminaste el componente `module` si existía?
4. ¿El selector de tipo del Node Editor está en Range Node Core?
