# Fase 6 — Módulos opcionales, ejecutable estable y accesos directos

## Elegir módulos

Pregúntame cuáles quiero con preguntas de selección múltiple (como mucho 4 opciones por pregunta:
agrúpalos en dos si hace falta). Marca como recomendados los que encajen con mi dominio y con la
máquina detectada y omite los que no apliquen (p. ej. los gráficos si no hay escritorio, o los que
necesitan una capacidad que el adaptador marca como no disponible: cada módulo dice cuáles necesita).

| Módulo | Archivo | Cuándo recomendarlo |
|---|---|---|
| M1 Elevación gráfica y red de seguridad | `modulos/01-elevacion-seguridad.md` | Linux con escritorio |
| M2 Navegador automatizable | `modulos/02-navegador.md` | Investigación, desarrollo web |
| M3 Personalidades auxiliares | `modulos/03-personalidades.md` | Investigación o roles distintos |
| M4 Interfaz gráfica | `modulos/04-interfaz.md` | Con escritorio y usuario poco de terminal |
| M5 Auditoría del sistema | `modulos/05-auditoria.md` | **Equipo virgen**: recomendado y el primero |
| M6 Permisos ampliados | `modulos/06-permisos.md` | Nunca recomendado, salvo equipo dedicado a reparar otros |
| M7 Modelo local pequeño | `modulos/07-modelo-local.md` | Solo si M4 hablará con un modelo local de 1-4B |
| M8 Agentes avanzados | `modulos/08-agentes-avanzados.md` | Uso intensivo de desarrollo o de delegación |

En un equipo virgen, M5 va primero porque sus hallazgos (sistema sin soporte, poca RAM, sin
escritorio) cambian qué otros encajan.

Para cada módulo elegido:
1. Lee **solo** su archivo.
2. Detecta si ya existe: si existe, verifícalo y complétalo, sin rehacerlo.
3. Constrúyelo según su especificación, con el código que encaje en esta máquina.
4. Pasa sus criterios de aceptación y anota la evidencia en `<CONFIG>/instalacion-claudio.md`.
5. Añade una línea en `## Módulos instalados` de `<INSTRUCCIONES>` (qué es y dónde vive su
   configuración) y una receta en `memoria/recetas/` con lo que costó resolver.

Los módulos no elegidos no se tocan, aunque ya estén instalados.

## Ejecutable estable (Linux, siempre, antes de los accesos directos)

Por qué: los accesos directos, los servicios y la interfaz no cargan el `.bashrc`. Si `<CLI>` cambia
de sitio con una actualización (típico con npm/nvm), todo eso deja de funcionar.

- Mira cómo está instalado (`command -v <CLI>`, `readlink` del ejecutable) y sigue lo que diga el
  adaptador (puede recomendar su instalación nativa).
- Si no hay una ruta fija que se mantenga sola, crea en `~/.local/bin/<CLI>` un lanzador que busque la
  versión más reciente instalada (nativa o npm, con o sin nvm) y la ejecute sin depender del PATH
  interactivo, guardando antes lo que hubiera.
- Nunca `curl | sh`: un instalador se descarga a un archivo, se revisa y se ejecuta.

Aceptación: `env -i HOME=$HOME PATH=/usr/bin:/bin ~/.local/bin/<CLI> --version` funciona (o la ruta
fija que dé el adaptador).

## Accesos directos (siempre que haya sesión gráfica)

En el Escritorio (`xdg-user-dir DESKTOP`): un acceso "<nombre> CLI" que abre `<CLI>` en una
terminal con la carpeta de proyectos como directorio de trabajo y, si M4 está instalado, otro que
abre la interfaz.

- Ruta absoluta del ejecutable, sin fijar una versión concreta; directorio de trabajo explícito.
- Márcalos como de confianza si el escritorio lo exige.
- Antes, mira los accesos que ya haya: si alguno ya abre el asistente o la interfaz, no crees un
  duplicado. No sobrescribas uno distinto: enséñame el diff.
- En otro sistema, el equivalente (macOS: un `.command`). Sin escritorio, dilo en el informe.

Aceptación: `desktop-file-validate` sin errores; lanzado de verdad (`gio launch <archivo>`), aparece
un proceso `<CLI>` nuevo cuyo `/proc/<pid>/cwd` es la carpeta de proyectos (ciérralo por su PID).
Que se ven en el Escritorio lo confirma el usuario.
