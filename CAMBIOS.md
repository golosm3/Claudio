# CAMBIOS

## v3.0

**Cambio de formato.** El kit deja de ser un único prompt con código de referencia adjunto y pasa a ser un
conjunto de especificaciones en Markdown, un archivo por apartado, que empieza por `promptinicial.md`.
**No lleva código**: cada instalación escribe el suyo, adaptado a su máquina, y lo que debe coincidir es
el comportamiento que piden los criterios de aceptación.

- **Distribución por GitHub**: las actualizaciones se buscan en las etiquetas publicadas del repositorio
  (`actualizar.md`, instalado como skill `actualizar`), no en un servidor local.
- **`MANIFIESTO.md`** con una versión por apartado: una actualización solo revisa lo que ha cambiado.
- **Universal, con adaptadores**: las fases y módulos hablan de capacidades y términos (`<CONFIG>`,
  `<INSTRUCCIONES>`, `<AJUSTES>`…), y `adaptadores/` los traduce para cada asistente de terminal. Claude
  Code es el recomendado y el único probado; Codex CLI, Gemini CLI y opencode tienen adaptador sin probar,
  y cualquier otro usa el adaptador genérico. Cada capacidad que falta tiene un plan B visible.
- **Renombrados**: `fases/03-claude-md.md` → `fases/03-instrucciones.md`, `fases/05-settings.md` →
  `fases/05-ajustes.md`, `plantillas/CLAUDE.md` → `plantillas/instrucciones.md`.
- **Progreso persistente** en `<CONFIG>/instalacion-claudio.md`: una instalación cortada se reanuda sin
  repetir preguntas.
- **Criterios de aceptación** en cada fase y módulo; la Fase 7 los repasa contra el manifiesto.
- **Poda de la base**: los subagentes base son `explorador` y `verificador`. `implementador`,
  `arquitecto-agentes` y las skills `orquestar` y `nuevo-agente` pasan al módulo opcional **M8 Agentes
  avanzados**. En instalaciones previas que ya los tengan, se conservan: no se borra nada.
- **Skill `actualizar`** nueva en la base.
- **Interfaz (M4)**: el aviso «kit vX disponible» consulta GitHub.
- **Verificación**: las pruebas de sesión usan el modo no interactivo del asistente (`<NO_INTERACTIVO>`)
  directamente, sin script auxiliar; si no lo tiene, verificación manual.
- **Sustituciones** (para la fusión de la Fase 3): en `<INSTRUCCIONES>`, la tabla de delegación pasa a
  usar niveles (económico/normal/potente) sustituidos por los modelos del adaptador, y a
  listar solo los agentes base, con una línea para los de M8 si está instalado. La sección del kit
  distribuible, si existía, es propia de quien distribuye y se conserva tal cual.

### Desde v2.x

Las instalaciones v2.x traían código copiado de las implementaciones de referencia (línea de estado,
hooks, interfaz, navegador). Es trabajo instalado y se conserva. La v3.0 lo compara con las
especificaciones nuevas y propone solo lo que falte.

Contenido heredado de la serie 2 que sigue vigente (ahora como especificación):
- actualización sin destruir;
- elevación gráfica y red de seguridad del hook;
- navegador automatizable;
- personalidades;
- interfaz con permisos como diálogos y seguridad de loopback;
- auditoría del sistema;
- permisos ampliados;
- guardianes para modelo local;
- línea de estado con compactado temprano y `opusplan` (v2.8).
