# M7 — Modelo local pequeño

Solo si la interfaz de M4 va a hablar con un **modelo local de 1-4B** (servido por `llama-server` u otro
servidor compatible) en vez de con un modelo grande en la nube. Con uno grande no se ofrece: sobra y
añade latencia.

Necesita: M4, hooks que bloquean, y que `<CLI>` pueda apuntar a un endpoint de modelo propio con su
propia carpeta de configuración (cómo, en el adaptador). Si falta algo de eso, no se ofrece.

## Por qué

Un modelo pequeño no obedece un prompt como uno grande. Se desboca escribiendo llamadas inventadas,
repite la misma herramienta en bucle, dice que ha hecho lo que no ha hecho y no converge en tareas de
varios pasos. Lo crítico se mueve a **código determinista**; el modelo solo rellena huecos con salida
forzada (gramática o `json_schema`).

## Qué construir (en el backend de la interfaz)

Se activa con una variable de entorno (p. ej. `<NOMBRE>_MODELO_LOCAL_URL` con la URL del endpoint de chat).
Sin ella, la interfaz se comporta exactamente igual que antes.

| Pieza | Qué hace |
|---|---|
| Clasificador charla/tarea | Lista de palabras de acción, asimétrica: ante la duda, tarea. Además, el modelo responde con salida forzada a una palabra (`charla\|tarea`), con unos pocos ejemplos |
| Charla directa | La charla se responde llamando al modelo directamente, **sin el CLI**. A través del CLI, un 2B gasta miles de tokens en llamadas inventadas para un «hola» |
| Guardián de bucles | Hook previo a cada herramienta (todas) en la carpeta de configuración propia del modelo local. Deniega la 3.ª llamada igual o casi igual (similitud de Jaccard ≥0,85), pone topes de exploración y de acciones por intercambio, y a la 3.ª denegación corta la sesión (en Claude Code, `continue:false`) |
| Forzar acción | Una tarea respondida sin ninguna herramienta se reintenta hasta 2 veces con una orden concreta; si sigue igual, se marca como `error`. No se da por hecha |
| Tareas por pasos | Plan con salida forzada (`json_schema`). Datos con nombre, incluidos los derivados; 1-6 pasos, cada uno con una comprobación de lista cerrada (`archivo_existe`, `carpeta_existe`, `no_existe`, `duracion_media`, `ninguna`). Un `<NO_INTERACTIVO>` por paso, comprobación en disco y 1 reintento con el fallo exacto |

Umbrales, textos y ejemplos en un archivo de configuración, no en el código. Con plantillas de
razonamiento, la salida forzada puede llegar en `reasoning_content`: lee ambos campos y desactiva el
pensamiento en clasificador, charla y planificador.

Entorno obligatorio del servicio:
- El CLI apunta al servidor local, con una clave ficticia y una carpeta de configuración propia (variables
  en el adaptador).
- Herramientas acotadas (leer, ejecutar, editar, escribir, buscar, leer web). El juego completo ocupa
  ~22K tokens y desborda un contexto de 32K.
- Tope de salida del CLI igual al límite real del modelo (p. ej. 4096). Sin este tope, el CLI
  pide ~32K y se han visto respuestas desbocadas hasta llenar el contexto.

## Criterios de aceptación

Por el WebSocket real de la interfaz, no importando módulos:
- «hola» → clasificado como charla, respuesta en <2 s.
- «crea una carpeta X en /tmp/…» → tarea; la carpeta existe.
- Una tarea de 2+ pasos (p. ej. un vídeo de N s a partir de M fotos con ffmpeg) → plan con pasos y
  comprobación en disco superada.
- 4 llamadas idénticas seguidas → las 2 últimas, bloqueadas por el guardián.
- Sin la variable de activación, la misma tarea da el mismo resultado que antes de instalar el módulo.
