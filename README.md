# Claudio

Kit para convertir un asistente de terminal recién instalado en un **asistente orquestador personal**. El
asistente vive en tu máquina, tiene memoria que aprende con el uso, delega en subagentes y se crea sus
propias herramientas.

**No contiene código.** Son especificaciones en Markdown: qué construir, por qué y cómo se comprueba.
Tu asistente escribe la implementación adaptada a tu máquina.

**Recomendado: Claude Code**, el de referencia y el único probado de principio a fin. También hay
adaptadores para Codex CLI, Gemini CLI y opencode (sin probar), y un procedimiento genérico para
cualquier otro: lo que el programa no permita se degrada de forma visible.

## Instalar

1. Descarga la última versión publicada (Releases → `vX.Y` → *Source code*) y descomprímela.
2. Abre tu asistente de terminal en esa carpeta y escribe:

   > Lee `promptinicial.md` y síguelo.

Sirve tanto para un equipo virgen como para una instalación previa, que se actualiza sin destruir nada:
copia de seguridad verificada, fusión y diffs con confirmación.

## Actualizar

Una vez instalado, pídele al asistente «busca actualizaciones del kit» (skill `actualizar`). Consulta
las versiones publicadas aquí y aplica solo los apartados que han cambiado, con tu confirmación.

**Si vienes de la v2.x** (kit en zip): tu instalación no busca en este repositorio, así que el primer
salto es manual. Descarga la última versión y sigue los pasos de instalación de arriba: se detecta como instalación
con marca anterior y se fusiona sin perder nada (el código que ya tengas copiado se conserva). Desde ahí,
las actualizaciones llegan de aquí.

## Estructura

| Ruta | Qué es |
|---|---|
| `promptinicial.md` | Punto de entrada |
| `MANIFIESTO.md` | Apartados, versión de cada uno y si es obligatorio |
| `fases/` | Fases 0-8 de la instalación |
| `adaptadores/` | Traducción de cada asistente de terminal (rutas, formatos, capacidades) |
| `plantillas/instrucciones.md` | Plantilla de las instrucciones permanentes |
| `modulos/` | Módulos opcionales (M1-M8) |
| `actualizar.md` | Flujo de actualización desde este repositorio |
| `CAMBIOS.md` | Cambios por versión |

## Requisitos

Un asistente de terminal con sesión iniciada que cargue instrucciones globales (Claude Code
recomendado). Pensado para Linux con escritorio; en macOS o Windows, las fases base
funcionan y algunos módulos se adaptan o no aplican.

## Seguridad

Cada instalación aplica los cambios en su máquina enseñando un diff y pidiendo confirmación. Solo se
usan las etiquetas publicadas, nunca la rama principal.
