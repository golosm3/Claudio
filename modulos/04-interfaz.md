# M4 — Interfaz gráfica de escritorio

Para: perfiles `linux`, `macos` y `windows` (necesita una ventana de navegador). En `terminal` no se ofrece.

## Por qué

Una ventana propia pensada para quien no vive en la terminal. Debe seguir siendo el mismo asistente,
con sus instrucciones, agentes, skills, hooks y memoria, por eso **envuelve el CLI** (`<CLI>`) en vez de
llamar a la API del modelo. Además dibuja lo que la terminal no puede: gráficas, diálogos de permiso y un panel del
sistema.

Necesita: modo no interactivo trazable **con entrada continua** y **canal de permisos programable**
(ver el adaptador). Si el programa no tiene ambos, M4 no se ofrece: sin canal de permisos, todo lo que
requiere confirmación se denegaría en silencio. Las opciones concretas de la orden (formato de eventos,
reanudar sesión, añadir al prompt del sistema, acotar herramientas) salen del adaptador; abajo se citan
las de Claude Code como ejemplo.

## Si ya existe una instalación

**No la pises**: puede tener adaptaciones del usuario.
- Detéctala por su servicio de usuario, sus lanzadores y su carpeta.
- Compara lo instalado con esta especificación y propón solo lo que falte, como cambios concretos que se
  aplican tras confirmación.
- No toques sus conversaciones, su carpeta de datos, su servicio ni sus lanzadores salvo con un diff
  aprobado.
- Conserva su nombre, carpeta, servicio y puerto. Antes de cambiar nada, comprueba que la copia de la
  Fase 2 incluye esa carpeta.

## Arquitectura

- **Backend**: un servidor local con el runtime que haya en la máquina. Lanza `<CLI>` en su modo no
  interactivo con eventos estructurados (en Claude Code: `claude -p --input-format stream-json
  --output-format stream-json --verbose`) y retransmite los eventos a la
  página por WebSocket.
- **Permisos y preguntas**: por el canal de permisos del adaptador (en Claude Code,
  `--permission-prompt-tool stdio`), cada petición de permiso y cada pregunta del CLI se muestra como **diálogo** en la página y la respuesta vuelve al CLI. Sin
  esto, lo que requiere confirmación se deniega en silencio.
- **Conversaciones**: cada una se guarda como JSON en la carpeta de datos de la interfaz y se reanuda con
  la opción de reanudar sesión del CLI (en Claude Code, `--resume <id de sesión>`).
- **Conversaciones en paralelo**: cambiar de conversación o abrir una nueva **no para** el trabajo de
  la anterior. Por eso el proceso del CLI no pertenece a la conexión WebSocket, sino a la conversación:
  - el backend lleva un registro de turnos en curso por conversación (proceso, estado y búfer de
    eventos); cerrar o recargar la página no mata nada;
  - cada evento se añade al JSON de la conversación según llega, no solo al terminar el turno;
  - la página se suscribe a la conversación que muestra; al volver a una que sigue en curso, o al
    recargar, recibe lo ya emitido y sigue en directo;
  - un solo turno a la vez por conversación; el botón «Detener» para solo el de esa conversación;
  - la lista de conversaciones marca las que están trabajando y las que esperan un permiso o una
    respuesta. Las peticiones de permiso de una conversación en segundo plano quedan en cola con aviso
    (el turno queda bloqueado hasta que se responden); nunca se aprueban ni se deniegan solas;
  - tope de conversaciones activas a la vez según la RAM detectada (cada proceso del CLI ocupa del
    orden de 250 MB; mídelo en la máquina), dentro del techo de memoria del servicio. Al llegar al
    tope, el turno nuevo espera en cola visible, no falla;
  - si dos conversaciones activas trabajan en la misma carpeta de proyecto, avisa de que pueden
    editar los mismos archivos.
- **Ventana**: Chrome/Chromium en modo `--app` si está instalado; si no, Firefox con un perfil dedicado.
  No instales un navegador solo para la ventana.
- **Arranque**:
  - el backend como servicio de usuario con techo de memoria proporcional a la RAM detectada;
  - la ventana como lanzador de escritorio;
  - pregunta si debe arrancar al iniciar sesión (autoarranque del escritorio).
- **Ruta de proyectos en un solo archivo de entorno**, que leen el servicio y los lanzadores; no repetida
  en cada uno.

## Seguridad (obligatorio)

- Escucha solo en `127.0.0.1`. Comprueba antes que el puerto esté libre (skill `puertos`).
- Valida `Host` y `Origin` en HTTP **y** en WebSocket.
- Token aleatorio por arranque, exigido en cada petición (cabecera, p. ej. `X-Token`). La página lo
  recibe al cargarse.
- Cabecera CSP restrictiva.
- Las rutas que puede leer o servir (árbol de archivos, adjuntos) están acotadas a la carpeta de
  proyectos. Los archivos de credenciales (`.env*`, claves, `~/.ssh`, `~/.aws`…) quedan vetados aunque
  estén dentro.
- El veto vale también para **las herramientas del CLI**, no solo para la API de la interfaz: el CLI
  autoaprueba lecturas dentro de la carpeta de trabajo sin pasar por el canal de permisos, así que un
  `Read` o un `cat` de un `.env` no llegaría nunca al diálogo. Aplícalo con el mecanismo del adaptador
  (en Claude Code, reglas `deny` o un hook `PreToolUse` que lea la misma lista de vetos que el backend).

## Funciones

| Función | Qué hace |
|---|---|
| Chat en streaming | Texto con Markdown completo (tablas, código resaltado). Herramientas usadas mostradas con su entrada, resultado y tiempo, plegadas; no se inventa lo que el CLI no expone |
| Gráficas | Un bloque de código con lenguaje `grafica` y JSON (`tipo` linea/barras/tarta, `titulo`, `etiquetas`, `series`, `destacados`) se dibuja en la página. El backend añade al prompt del sistema (en Claude Code, `--append-system-prompt`) cómo pedirlas |
| Archivos | Los archivos que el CLI crea o edita aparecen como tarjeta descargable; adjuntar archivos al mensaje |
| Árbol de proyectos | La raíz es la carpeta de proyectos de la instalación (`{{PROYECTOS}}`), leída del archivo de entorno del servicio, nunca una ruta fija en el código; al pasar el cursor, el contenido de su `DESCRIPCION.md` como tooltip |
| Personalidades | Elegir una de `personalidades/` (se añade al prompt del sistema); crear una nueva por entrevista |
| Skills | Sin botones fijos en la bienvenida. «Añadir skill»: el asistente entrevista (con preguntas de opciones si el CLI las tiene) y crea la skill en `<SKILLS>` (o el procedimiento, según el plan B). «Elegir skills»: el usuario marca cuáles salen como botón (hasta 12), con frases cortas por skill guardadas en la carpeta de datos (una reinstalación no las pisa) |
| Comandos `/` | Autocompletado de skills y comandos del CLI |
| Sesiones | Lista de las sesiones del CLI (`<SESIONES>`, si son legibles) para reanudar también las abiertas desde la terminal |
| Búsqueda y exportación | Buscar en conversaciones; exportar una a Markdown |
| Panel del sistema | CPU, RAM, disco, GPU si la hay, y estado del servicio |
| Límites | Uso de sesión y semana si el CLI los expone; coste por conversación |
| Herramientas del CLI | Si el CLI permite acotar herramientas por orden (en Claude Code, `--tools`): vacío = todas; `ninguna` = solo charla (ahorra ~20K tokens de esquemas por turno) |
| Voz (opcional) | Lectura en voz alta con un TTS local o libre si el usuario lo quiere |
| Remote Control | Si la versión del CLI lo admite **en modo no interactivo** (compruébalo enviando la orden; en algunas versiones responde que no está disponible), botón para activarlo en la conversación; si no, el botón no aparece |
| Modelo y esfuerzo | Selector por conversación con los modelos que admite el CLI (y los de un backend propio si lo hay); el elegido se pasa al lanzar el turno |
| Agentes | Lista de los subagentes instalados con su descripción; al pulsar uno, se inserta un encargo para él |
| Ajustes | Panel con lo configurable (nombre, carpeta de proyectos, tope de conversaciones en paralelo, tema), guardado en la carpeta de datos |
| Kit disponible | Al arrancar y una vez al día, consulta la última versión publicada del repositorio del kit (ver `actualizar.md`). Si es mayor que la instalada, muestra un botón «kit vX.Y disponible» que pide al asistente ejecutar la skill `actualizar`. Si no hay marca de instalación, el botón es «Instalar Claudio». Antes de lanzarlo, el botón explica qué hace: instala o actualiza el kit (las instrucciones, skills y agentes del asistente), no la aplicación; fusiona con lo existente tras una copia de seguridad; y desaparece cuando la marca coincide con la última versión. Nunca aplica nada solo |

**Aspecto (mínimo exigible)**: que funcione no basta; una interfaz de una sola columna con estilos
por defecto es la queja habitual. Como mínimo:
- barra lateral plegable (conversaciones con búsqueda, proyectos, personalidades, agentes) y zona de chat
  con el cuadro de entrada fijo abajo;
- tema claro y oscuro (por defecto el del sistema), tipografía legible y código con resaltado;
- estados visibles: escribiendo, herramienta en curso con su tiempo, en cola, esperando permiso, error;
- usable a partir de 360 px de ancho (barra lateral como cajón) y con teclado (Intro envía,
  Mayús+Intro salto de línea, Esc cierra diálogos);
- sin librerías desde una CDN: lo que haga falta va servido localmente (la CSP lo exige).

**Cómo construirla**: es el módulo más grande del kit (una interfaz completa ronda varios miles de
líneas). No la escribas al final de una sesión larga de instalación:
- constrúyela en una sesión o subagente dedicado, que reciba esta especificación y los datos de la
  máquina, no el historial de la instalación;
- por etapas, cada una probada antes de pasar a la siguiente:
  1. backend, seguridad, chat en streaming y permisos;
  2. conversaciones en paralelo y reanudación;
  3. archivos, gráficas, árbol y aspecto;
  4. el resto de la tabla de funciones;
- termina con un informe que tenga una fila por función de la tabla: **hecha**, **parcial** o **no
  hecha**, con la prueba que lo demuestra. Las parciales y no hechas se le dicen al usuario y quedan en
  el `ESTADO.md` de la interfaz; no se presentan como terminadas.

**Interfaz heredada (v2.x) con actualización por servidor**: las interfaces de la serie 2 traían dos
avisos que leían de un servidor configurado (`<servidor>/…/version.json` para actualizarse a sí mismas y
`…/kit/kit.json` para el kit). En la v3 la interfaz no se distribuye y el kit vive en el repositorio, así
que al instalar o actualizar sobre una interfaz así:
- el aviso del kit pasa a consultar el repositorio, como dice la fila «Kit disponible», y su botón pide
  la skill `actualizar`; ya no descarga nada del servidor;
- la autoactualización de la propia interfaz desde el servidor se desactiva (botón oculto, ajuste
  vacío), salvo que el usuario diga que él mismo publica en ese servidor; en ese caso se conserva, pero
  separada del aviso del kit;
- criterio: con el ajuste del servidor vacío o sin red local, la interfaz no muestra ningún aviso de
  ese servidor, y el aviso del kit compara la marca instalada con la última etiqueta del repositorio.

**Nombre**: todo lo visible (título, marca, saludo, lanzadores, notificaciones) lleva el nombre del
asistente, nunca el del programa ni el del modelo. En una instalación nueva, la carpeta, el servicio y el comando se llaman
`<nombre>-ui`, en minúsculas y sin acentos. El nombre vive en la configuración de la interfaz, no
escrito en el código.

**Modelo servido por un backend propio** (proxy, LiteLLM, llama-server): fija en el entorno del servicio
el tope de salida del CLI al límite real del modelo (variable en el adaptador). Si no, el CLI puede pedir
más de lo que el servidor admite.

**Mover la carpeta de proyectos sin perder sesiones** (si `<SESIONES>` va por ruta de trabajo, como en
Claude Code: `~/.claude/projects/<ruta con / → ->`). Si la carpeta cambia de sitio:
1. copia y sha256 de todo;
2. la ruta vieja pasa a ser un enlace a la nueva;
3. crea en `<SESIONES>` el enlace `<ruta-nueva>` → `<ruta-vieja>`;
4. cambia la ruta en el archivo de entorno único.

## Criterios de aceptación

Pruébala con un **navegador automatizado** recorriendo el flujo real (M2 si está instalado):
- La página carga en el puerto local; desde otro `Origin` o sin token, HTTP y WebSocket se rechazan.
- Enviar «hola» devuelve respuesta en streaming con el nombre del asistente.
- Una petición que necesite permiso muestra el diálogo, y al aceptarlo la herramienta se ejecuta.
- Una respuesta con un bloque `grafica` se dibuja (hay un `canvas`/`svg` en la página).
- Reanudar una conversación tras reiniciar el servicio conserva el contexto.
- Intentar leer `~/.ssh` o un `.env` por la interfaz se rechaza, y pedirle al asistente que lea un
  `.env` de un proyecto (con su herramienta de lectura y por shell) también se bloquea.
- **Paralelo**: con un turno largo en curso en la conversación A (p. ej. «cuenta hasta 30 despacio con
  una orden de shell»), abrir la B y enviar un mensaje funciona; A sigue trabajando (su JSON crece) y al
  volver a A se ve el resultado completo. Recargar la página durante el turno de A no lo corta.
- Una petición de permiso de una conversación en segundo plano aparece marcada en la lista y se puede
  responder al abrirla.
- Superar el tope de conversaciones activas deja el turno en cola visible, sin error.
- Cada función de la tabla tiene su comprobación en el informe final (hecha / parcial / no hecha).
- El servicio sobrevive a cerrar la ventana y tiene techo de memoria (`systemctl --user show -p MemoryMax`).

Las pruebas que crean artefactos (conversaciones, skills) llevan copia previa y limpieza. El
autoarranque solo se confirma reiniciando la sesión: dilo en vez de darlo por probado. Que la ventana se
ve bien lo confirma el usuario.
