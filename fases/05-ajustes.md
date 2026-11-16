# Fase 5 — Ajustes, línea de estado y modelo

Claves y formatos concretos: los del adaptador. Lo que su programa no tenga se resuelve con el plan B
de `adaptadores/README.md` y se anota en el progreso como `omitido (el programa no lo permite)`.

## Fusión, nunca reemplazo

Todo lo que se añada a `<AJUSTES>` (aquí y en los módulos): lee el archivo existente, fusiona claves
y listas sin duplicar ni perder entradas, valida el resultado en su formato (JSON, TOML…) y enséñame
el diff antes de guardarlo. Si el archivo existente no es válido, no lo toques: dímelo.

## Línea de estado (pieza base si el programa la admite)

Por qué: los hilos maratón. Medido en uso real, las sesiones de más de 200 turnos concentraban la
mayor parte del gasto, porque cada turno relee todo el contexto. La línea hace visible ese coste en
cada respuesta y avisa de cuándo cerrar el hilo.

Qué construir: un script sin dependencias (en el lenguaje que ya haya en la máquina; Python 3 si
existe) en `<CONFIG>/`, configurado como línea de estado del programa con ruta absoluta. El programa
le pasa datos de la sesión (el adaptador dice cuáles y por dónde). No supongas los nombres de campo:
captura una entrada real antes de programar. Para ello, configura primero una línea provisional que
solo vuelque su entrada a un archivo; si no puedes provocar una sesión interactiva, pide al usuario que
abra `<CLI>` y envíe un mensaje. Usa esa entrada como caso de prueba del script. Si no hay forma de
capturarla, apóyate en la documentación oficial y márcalo en el informe como «sin verificar con
entrada real».

Muestra en una línea lo que los datos permitan:

- modelo abreviado;
- `ctx N%` con barra: tokens de entrada del último turno respecto a la ventana de compactado (o al
  tamaño de contexto si no está). Amarillo ≥75 %, rojo ≥90 %;
- `N turnos`: respuestas del modelo contadas en la transcripción. Amarillo ≥100, rojo ≥200;
- coste estimado de la sesión;
- límites de suscripción (sesión y semana, con hora de reinicio) si llegan; si no llegan, no los
  inventes;
- una segunda línea solo cuando toca: `⚠ hilo largo` (amarillo) o `⚠ cierra el hilo: anota en
  ESTADO.md y abre uno nuevo` (rojo).

Umbrales ajustables por entorno (`LINEA_ESTADO_TURNOS=100,200`, `LINEA_ESTADO_PCT=75,90`,
`NO_COLOR=1`). Un error (entrada ilegible, transcripción inaccesible) se muestra en la propia línea;
nunca se traga. Escribe sus pruebas (entradas de ejemplo → salida esperada, incluidos los casos sin
límites de suscripción y con error) y pásalas antes de activarlo. Si ya hay una línea de estado del
usuario, no la pises: propónmelo como diff.

Sin línea de estado en el programa: plan B (regla de avisar de cerrar el hilo pasados ~100 turnos).

## Compactado

Si el programa lo permite, compacta el contexto en torno a **150 000 tokens** (antes de que cada turno
pese ~200k). Clave en el adaptador.

## Modelo

Hilo principal: si el programa permite un modelo potente para planificar y uno normal para ejecutar,
úsalo (valor en el adaptador); si no, el nivel normal. Solo si el usuario no tiene ya un modelo
fijado; si lo tiene, propónlo como diff y explica el coste relativo.

## Criterios de aceptación

- `<AJUSTES>` es válido y conserva todas las claves previas.
- Las pruebas del script de la línea de estado pasan (si existe).
- Compactado y modelo comprobados con las órdenes del adaptador.
- Que la línea se ve bajo el prompt de `<CLI>` lo confirma el usuario (es visual).
