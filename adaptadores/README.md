# Adaptadores — un núcleo, varios asistentes de terminal

Las fases y los módulos están escritos en términos de **capacidades**, no de un programa concreto.
Cada adaptador traduce esas capacidades a las rutas, formatos y órdenes de un asistente de terminal
(Claude Code, Codex CLI, Gemini CLI, opencode…). El kit recomienda **Claude Code**: es el adaptador de
referencia y el único con la instalación probada de principio a fin. Los demás funcionan con lo que su
programa permita, y lo que no, se degrada de forma visible.

## Qué asistente está ejecutando el kit (Fase 0)

Por este orden, y anota en el progreso de dónde salió:
1. Lo que sabes de ti mismo (tus instrucciones de sistema suelen nombrar el programa).
2. Variables de entorno de tu propia shell: `AI_AGENT` (p. ej. `claude-code_…`), `CLAUDECODE=1`, y las
   que documente cada adaptador.
3. Si sigue sin estar claro, pregúntalo, con los adaptadores existentes como opciones.

Si existe `adaptadores/<programa>.md`, léelo y úsalo. Si no, aplica el **adaptador genérico** (abajo).
Si el programa no es Claude Code, díselo al usuario una vez, en una línea: qué capacidades quedarán
degradadas según la tabla del adaptador y que Claude Code es la opción recomendada. Después sigue con
el programa que tiene; no insistas.

## Términos que usan las fases

Cada adaptador da el valor de cada término. En el texto de las fases aparecen entre ángulos.

| Término | Qué es |
|---|---|
| `<CLI>` | Ejecutable del asistente |
| `<CONFIG>` | Carpeta de configuración del usuario del asistente. Aquí viven también `memoria/`, `backups/`, `kit/` e `instalacion-claudio.md` |
| `<INSTRUCCIONES>` | Archivo de instrucciones globales que el asistente carga en cada sesión |
| `<INSTR_LOCAL>` | Nombre del archivo de instrucciones por carpeta (se carga al trabajar dentro de ella) |
| `<AJUSTES>` | Archivo de ajustes del asistente (modelo, permisos, hooks…) |
| `<AGENTES>` | Dónde y cómo se definen subagentes |
| `<SKILLS>` | Dónde y cómo se definen skills (conocimiento o pasos que se cargan cuando hacen falta) |
| `<NO_INTERACTIVO>` | Orden para lanzar un encargo sin interfaz y obtener una salida trazable (herramientas usadas, respuesta, coste si lo da) |
| `<SESIONES>` | Dónde guarda el historial de sesiones, si es legible |

## Capacidades

| Capacidad | Para qué la usa el kit | Plan B si el programa no la tiene |
|---|---|---|
| Instrucciones globales | Identidad, reglas y memoria (Fase 3) | Imprescindible: sin ella el kit no puede instalarse. Dilo y para |
| Subagentes | Delegar barridos y verificación (Fase 4, M8) | Las secciones de delegación pasan a decir «hazlo en el hilo principal, acotando la salida». No se crean agentes |
| Skills | `memoria`, `puertos`, `actualizar`, M8 | Cada skill se guarda como `.md` en `<CONFIG>/procedimientos/` y `<INSTRUCCIONES>` dice cuándo leer cada uno (una línea por procedimiento) |
| Hooks que bloquean órdenes | Red de seguridad de M1 | La lista de órdenes vetadas pasa a texto en `<INSTRUCCIONES>` y el informe dice claramente que nada la hace cumplir |
| Línea de estado por script | Coste y longitud del hilo (Fase 5) | Una regla en `<INSTRUCCIONES>`: avisar de cerrar el hilo pasados ~100 turnos. Informe: «sin línea de estado» |
| Compactado configurable | Compactar antes (Fase 5) | Se omite y se dice |
| Modelo por nivel | Barato para barridos, potente para planificar | Usa el modelo por defecto del programa; los niveles de la tabla de delegación se omiten |
| Modo no interactivo trazable | Verificación (Fase 7), interfaz (M4) | Verificación manual: el usuario abre `<CLI>` y pega la respuesta. M4 no se ofrece |
| Canal de permisos programable | Diálogos de permiso en la interfaz (M4) | M4 no se ofrece |

## Adaptador genérico (programa sin adaptador en el kit)

1. Busca la documentación oficial del programa (su web o su repositorio) y rellena la tabla de términos
   y la de capacidades, con la URL de cada dato. No rellenes nada de memoria: lo que no esté
   documentado, compruébalo en la máquina (`<CLI> --help`, archivos de su carpeta) o márcalo como
   «no disponible».
2. Enseña la tabla al usuario antes de usarla.
3. Guárdala en `<CONFIG>/kit/adaptador-local.md` para las actualizaciones. Si el usuario quiere
   compartirla, puede proponerla al repositorio del kit.

## Formato de un adaptador

`adaptadores/<programa>.md` contiene: estado (probado o sin probar, y con qué versión del programa),
cómo detectarlo, la tabla de términos con valores, la tabla de capacidades (disponible / no disponible
/ parcial, con cómo se hace), los formatos concretos (frontmatter de agentes y skills, claves de
ajustes) y las órdenes de verificación de cada criterio de aceptación que dependa del programa.
