```text
███╗   ███╗███╗   ███╗██████╗
████╗ ████║████╗ ████║██╔══██╗
██╔████╔██║██╔████╔██║██║  ██║
██║╚██╔╝██║██║╚██╔╝██║██║  ██║
██║ ╚═╝ ██║██║ ╚═╝ ██║██████╔╝
╚═╝     ╚═╝╚═╝     ╚═╝╚═════╝

 ██████╗  █████╗ ███╗   ██╗████████╗████████╗
██╔════╝ ██╔══██╗████╗  ██║╚══██╔══╝╚══██╔══╝
██║  ███╗███████║██╔██╗ ██║   ██║      ██║
██║   ██║██╔══██║██║╚██╗██║   ██║      ██║
╚██████╔╝██║  ██║██║ ╚████║   ██║      ██║
 ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═══╝   ╚═╝      ╚═╝

███████╗████████╗██╗   ██╗██████╗ ██╗ ██████╗
██╔════╝╚══██╔══╝██║   ██║██╔══██╗██║██╔═══██╗
███████╗   ██║   ██║   ██║██║  ██║██║██║   ██║
╚════██║   ██║   ██║   ██║██║  ██║██║██║   ██║
███████║   ██║   ╚██████╔╝██████╔╝██║╚██████╔╝
╚══════╝   ╚═╝    ╚═════╝ ╚═════╝ ╚═╝ ╚═════╝
```

**▶ Demo en vivo: [robrstein.github.io/mmd-gantt-studio](https://robrstein.github.io/mmd-gantt-studio/)**

Visor y editor de diagramas de Gantt escritos en sintaxis [Mermaid](https://mermaid.js.org/syntax/gantt.html), pensado para gestionar proyectos con **un gantt general y múltiples subproyectos de detalle**, todo en el navegador sin backend ni build step.

## Idea

Muchos proyectos documentan su cronograma como un `.md` con varios diagramas Mermaid: uno general (resumen de todo el proyecto) y varios de detalle (uno por área/etapa), enlazados entre sí. Editar y visualizar eso a mano es incómodo. mdGanttViewer resuelve esto con:

- **Panel izquierdo:** editor de texto plano con la sintaxis Mermaid del gantt activo.
- **Panel derecho:** dos vistas intercambiables del mismo diagrama:
  - **🖱 Interactiva** (por defecto): editor visual estilo MS Project sobre [Frappe Gantt](https://frappe.io/gantt) — arrastra una barra para moverla, estira sus bordes para cambiar la duración, arrastra el % de progreso para marcar `done`/`active`, clic para abrir el formulario de edición. Cada gesto reescribe **solo la línea afectada** del texto Mermaid (que sigue siendo la única fuente de la verdad) y la destella en el editor. Incluye tabla lateral de tareas, cabecera de fechas fija al hacer scroll, escala Día/Semana/Mes, secciones colapsables (flecha ▶/▼ por sección o casilla «Agrupar secciones») cuya fila de resumen muestra avance ponderado, tareas completadas y críticas — y las flechas de dependencia entrantes/salientes de sus tareas ocultas se redirigen a la propia sección —, botón «＋ Tarea» y avisos con «Deshacer».
  - **📄 Mermaid**: renderizado clásico de Mermaid con zoom y paneo (doble clic sobre una tarea para editarla).
- **Navegación:** desde el gantt general, un clic en una tarea, en una sección, o en el sidebar de subproyectos, abre el diagrama de detalle correspondiente.
- **Persistencia:** todo se guarda en `localStorage` del navegador — múltiples proyectos, cada uno con su gantt general y sus subproyectos.

## Estado del proyecto

Versión funcional en [`index.html`](./index.html): se abre directo en el navegador, sin instalación. El diseño de funcionalidades está en [`roadmap.md`](./roadmap.md).

## Objetivo técnico

- Aplicación de una sola página (`index.html`), sin build step — se abre directo en el navegador.
- [Mermaid.js](https://mermaid.js.org/) y [Frappe Gantt](https://github.com/frappe/gantt) (MIT) cargados vía CDN.
- Sin backend: todo el estado vive en `localStorage`, con import/export a `.md` y a `.json` para respaldo o migración entre navegadores.

## Reglas de la edición visual (vista interactiva)

Para que el texto Mermaid nunca se "destroce" al arrastrar:

- Solo se reescribe la línea de la tarea afectada; comentarios, orden y formato del resto del texto se conservan.
- Mover una tarea con fecha explícita cambia la fecha y conserva la duración.
- **Estirar** una tarea con `after otraTarea` conserva la dependencia y solo ajusta la duración; **moverla** la convierte a fecha fija (con aviso y opción de deshacer).
- Las duraciones se recalculan en días laborables si hay `excludes` (p. ej. `weekends`), y el inicio salta los días excluidos.
- Los milestones solo cambian de fecha.
- Arrastrar el % de progreso mapea a los estados de Mermaid: ≥90% → `done`, intermedio → `active`, ~0% → sin estado.

## Funcionalidades planeadas

Ver el detalle completo, organizado por fases, en [`roadmap.md`](./roadmap.md). Resumen del MVP (Fase 1):

- Crear, renombrar y eliminar proyectos y subproyectos. La creación es instantánea (sin diálogo) y el nombre está sincronizado con el `title` del diagrama: escribir el `title` (o pegar contenido con otro) renombra en vivo, y renombrar desde el botón ✎ reescribe el `title`.
- Edición de texto Mermaid con renderizado en vivo.
- Vincular tareas/secciones del gantt general con un subproyecto de detalle.
- Navegación por clic (tarea, sección o sidebar) entre el gantt general y sus detalles.
- Importar/exportar diagramas individuales en `.md`.
- Importar/exportar todo el estado (todos los proyectos) como respaldo `.json`.

## Ejemplo de referencia

[`gantt.md`](./gantt.md) contiene un caso real de uso: el cronograma de un proyecto (AlertaPPU) con un diagrama general y 6 diagramas de detalle (Fundamentos, Backend, Frontend, Testing/QA, Despliegue, Roadmap post-MVP), todos en un mismo archivo Markdown con bloques ```mermaid``` independientes. Es el formato que este visor busca poder abrir, editar y navegar.

## Uso

Abrir la versión publicada en [https://robrstein.github.io/mmd-gantt-sutdio/](https://robrstein.github.io/mmd-gantt-sutdio/), o clonar el repo y abrir [`index.html`](./index.html) directo en el navegador — sin instalación ni build step.

## Licencia

[MIT](./LICENSE)
