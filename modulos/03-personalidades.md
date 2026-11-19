# M3 — Personalidades auxiliares

Para: todos los perfiles.

Necesita: instrucciones por carpeta (`<INSTR_LOCAL>`). Si el programa solo carga las globales, la regla
«hablar con <nombre>» va en `<INSTRUCCIONES>` con la ruta absoluta de `personalidades/`.

## Por qué

Algunas tareas piden otro método y otro formato, no otro asistente. Investigar contrastando fuentes es
un ejemplo. Una personalidad es un papel que el asistente adopta en la misma conversación. Tiene su
propia identidad, método y estado, y se puede volver al asistente principal en cualquier momento.

## Qué construir

- Carpeta `personalidades/` en la carpeta de proyectos, con un `README.md` que explique la estructura y
  tenga una tabla de las personalidades (nombre, para qué, carpeta).
- Una subcarpeta autocontenida por personalidad con:
  - `<INSTR_LOCAL>`: identidad, método y formato de respuesta;
  - archivos de configuración editables (p. ej. `fuentes.md`);
  - `ESTADO.md` y `DECISIONES.md`.
- En el `<INSTR_LOCAL>` de la carpeta de proyectos (configuración local, no el global), la regla «hablar con
  <nombre>»:
  1. Resolver el nombre contra las carpetas, sin tildes ni mayúsculas. Si no existe, listar las
     disponibles y no inventar ninguna.
  2. Leer sus `.md` (salvo `DECISIONES.md`, solo si hace falta el porqué) y adoptar su identidad,
     método y formato en la misma conversación.
  3. Seguir en ese papel hasta que se pida volver.
  4. Al volver: resumir en pocas líneas y actualizar su `ESTADO.md`.
- Una personalidad por defecto, **investigadora**, con este método:
  - contrastar al menos dos fuentes independientes, o una primaria inequívoca;
  - leer la fuente, no el resumen;
  - separar lo verificado de lo supuesto;
  - dar un nivel de confianza.

  Su nombre y sus fuentes preferidas se definen por entrevista con el usuario (opciones concretas, no un
  formulario). Las personalidades nuevas se crean igual, siguiendo el README.
- Si ya hay personalidades, no las modifiques.

## Criterios de aceptación

- `<NO_INTERACTIVO>` con «hablar con <nombre de la investigadora>: ¿quién eres y cómo trabajas?», lanzado en la carpeta
  de proyectos, responde con su identidad y su método.
- Con un nombre inexistente, lista las disponibles y no inventa ninguna.
