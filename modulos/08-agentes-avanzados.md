# M8 — Agentes avanzados

Para: todos los perfiles.

## Por qué

La base trae solo `explorador` y `verificador`: delegar cuesta (un subagente arranca frío) y para un uso
ligero no compensa. Con desarrollo intensivo sí compensa separar quien diseña (caro) de quien ejecuta
(barato), y que el asistente pueda crearse perfiles nuevos.

Necesita: subagentes (sin ellos no se ofrece; las skills solas no compensan) y, para que tenga sentido
el reparto de coste, modelo por nivel. Formatos de agente y skill, en el adaptador.

## Qué construir

**Agentes** en `<AGENTES>`, sin la herramienta de lanzar agentes (un agente nunca lanza a otro):

| Agente | Nivel / esfuerzo | Herramientas | Qué hace |
|---|---|---|---|
| `implementador` | normal / medio | leer, editar, escribir, buscar, ejecutar | Cambio acotado (1-5 archivos) ya localizado y especificado por quien lo invoca. No explora ni decide arquitectura |
| `arquitecto-agentes` | potente / alto | leer, escribir, editar, buscar, ejecutar | Crea `.md` de agente o skill con frontmatter correcto, nombrado por función y no por proyecto, y lo prueba con un encargo real antes de darlo por bueno |

**Skills** en `<SKILLS>` (o procedimientos, según el plan B):
- `orquestar`:
  - cuándo delegar en paralelo, en serie o en el hilo principal;
  - regla: «delegar solo lo que ensuciaría el contexto con material voluminoso»;
  - tabla tarea → agente → nivel/esfuerzo: explorador económico/bajo; implementador y verificador
    normal/medio; arquitecto potente/alto; hilo principal y modelos caros a petición, según el
    adaptador (valores concretos sustituidos al instalar);
  - si un agente falla, repetir un nivel por encima (económico → normal → potente);
  - cómo redactar un encargo completo: contexto, qué se descartó, formato de respuesta, y que el agente
    no puede lanzar otros;
  - el **plan autocontenido**: el hilo principal escribe `<proyecto>/plans/<fecha>-<tema>.md` sin tocar
    código, para el ejecutor más barato posible. Lleva:
    - estado actual con extractos;
    - convenciones con un ejemplo;
    - pasos, cada uno con una puerta verificable;
    - límites y condiciones de parada;
    - sello de fecha/commit.

    `implementador` recibe solo la ruta del plan.
- `nuevo-agente`: cómo decidir si una necesidad es un agente (rol con criterio propio y herramientas
  propias), un skill (una lista de pasos o conocimiento) o ninguna de las dos (una secuencia simple).

Añade la fila de estos agentes a la tabla de delegación de `<INSTRUCCIONES>` (la plantilla ya prevé la línea
para cuando el módulo está instalado). No sobrescribas agentes ni skills con el mismo nombre: propón diff.

Dónde escribe `arquitecto-agentes`: algunos programas no dejan a un subagente escribir en `<CONFIG>`
(ruta protegida, sobre todo en modo no interactivo; ver el adaptador). Entonces escribe en una carpeta
de preparación dentro de la carpeta de proyectos (`{{PROYECTOS}}`) y el hilo principal revisa y copia el resultado a `<AGENTES>` o
`<SKILLS>`. Un agente o skill creado en una sesión no siempre se ve en esa misma sesión: pruébalo
en una sesión nueva.

## Criterios de aceptación

- Los dos agentes y las dos skills existen, con formato válido según el adaptador y sin la herramienta de lanzar agentes.
- Un encargo real a `arquitecto-agentes` (p. ej. «crea un skill para comprobar el espacio en disco antes de
  un build») produce un skill que una sesión nueva usa sin que se lo nombren. Bórralo después: era de
  prueba. Los proyectos que se creen para probarlo van dentro de la carpeta de proyectos (`{{PROYECTOS}}`) y se borran también.
- Un plan autocontenido pequeño ejecutado por `implementador` pasa sus puertas.
