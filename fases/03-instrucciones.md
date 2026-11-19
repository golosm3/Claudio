# Fase 3 — Instrucciones globales (`<INSTRUCCIONES>`)

La plantilla es `plantillas/instrucciones.md`; el resultado va en `<INSTRUCCIONES>`: sustituye los `{{…}}` con los datos de la Fase 1. La
sección `## Módulos instalados` la rellena la Fase 6.

La plantilla nombra capacidades (subagentes, skills, hooks, línea de estado, modelos por nivel) y los
términos `<…>` del adaptador. Sustituye los términos por sus valores y, para cada capacidad que el
adaptador marque como no disponible, redacta la sección según su plan B (`adaptadores/README.md`):
el resultado no puede pedir al asistente algo que su programa no sabe hacer.

**Bloques por plataforma.** Algunas partes de la plantilla van entre
`<!-- perfiles: … -->` y `<!-- /perfiles -->`. Conserva solo los bloques cuya lista incluye el
perfil de plataforma de la Fase 0 (`linux`, `macos`, `windows` o `terminal`), quita los demás
enteros y borra los marcadores. Lo que no lleva marcador vale para todos los perfiles. Si en la
fusión una regla existente pertenece a otro perfil, no la quites sin enseñármela.

- **Virgen**: créalo con la plantilla, adaptada con mis datos.
- **Previa o con marca**: fusiona sección a sección.
  - Conserva todo lo que ya hay: mis reglas, mi redacción y mis secciones propias,
    aunque no estén en la plantilla.
  - Añade solo las reglas y secciones de la plantilla que falten o que estén cubiertas
    solo en parte.
  - Si una regla existente contradice la plantilla, manda la existente. No la cambies:
    enséñame las dos versiones para que decida yo.
  - Mantén la numeración coherente y no dupliques reglas con otras palabras.
  - Si la plantilla sustituye o renombra una sección (lo dice `CAMBIOS.md`), no añadas la nueva junto a la vieja: propón el cambio de una por otra como
    línea que cambia, con las dos versiones a la vista.
  - Prepara la fusión en un archivo aparte (`<INSTRUCCIONES>.propuesta`), sin tocar el original.
    Enséñame literalmente toda línea existente que cambie o desaparezca. Lo que solo se
    añade puede ir resumido, porque el texto completo queda en la propuesta y puedo
    abrirla. Ancla cada inserción en texto existente y comprueba con `diff` que ninguna
    línea existente cambia o desaparece salvo las que me enseñas. Pide confirmación una
    sola vez y, al aplicar, borra la propuesta.
- En todos los casos, la primera línea del resultado debe ser la marca de versión de la
  plantilla.

