# Fase 4 — Memoria, agentes y skills base (solo lo que falte)

Formatos concretos (frontmatter, rutas, niveles de modelo): los del adaptador. Si el programa no tiene
subagentes o skills, aplica el plan B de `adaptadores/README.md` y anótalo en el progreso.

## Vault de memoria (`<CONFIG>/memoria/`)

Qué: notas markdown con `[[enlaces]]`, en `agentes/`, `proyectos/`, `recetas/`, `errores/`, más un
`INDICE.md` con una línea por nota (`- [[carpeta/nombre]] — resumen`).
Por qué: el asistente aprende con el uso; sin esto repite diagnósticos y montajes ya resueltos.

- Crea solo las carpetas que falten (con `.gitkeep` si quedan vacías).
- Si falta `INDICE.md`, créalo con 2-3 líneas sobre el propósito del vault y el formato. Si existe,
  no lo reescribas: añade solo las notas que existen y no estén indexadas.
- Nunca modifiques ni borres una nota.
- Si la Fase 0 encontró una instalación del kit en la carpeta de otro asistente, ofrece reutilizar su
  `memoria/`: enlace simbólico (las dos instalaciones comparten lo aprendido) o copia. Pregúntalo una
  vez; por defecto, enlace.

## Subagentes base (`<AGENTES>`)

Cada uno lleva nombre, descripción (cuándo usarlo), herramientas permitidas, nivel de modelo y unas
instrucciones. **Ninguno puede lanzar otros agentes** (cómo se impide lo dice el adaptador) y su
cuerpo lo dice: si necesita otro barrido, lo devuelve como pendiente.

| Agente | Nivel de modelo / esfuerzo | Herramientas | Qué hace |
|---|---|---|---|
| `explorador` | económico / bajo | leer, buscar, shell de solo lectura | Localiza código, config o logs. Devuelve tabla `archivo:línea`; prohibido añadir conclusiones no pedidas |
| `verificador` | normal / medio | shell, leer, buscar | Ejecuta la app o función real y devuelve evidencia; no modifica código. Con el nivel económico da por buena evidencia que no lo es |

`implementador` y `arquitecto-agentes` no son base: van en el módulo 8. La regla es delegar solo lo
que vaya a tragar material voluminoso (un subagente arranca frío y para trabajo pequeño sale más caro).

## Skills base (`<SKILLS>`)

La descripción de cada skill dice **cuándo** usarla, para que se active sola.

| Skill | Contenido que debe cubrir |
|---|---|
| `memoria` | Consultar el vault antes de repetir un diagnóstico o un montaje; cómo guardar una nota: tipo, cuándo merece la pena, formato, actualizar antes que crear, añadirla al índice |
| `puertos` | Antes de que algo escuche en un puerto TCP: comprobar que está libre (`ss -ltnp` o `lsof`), escuchar solo en `127.0.0.1`, puerto desde configuración o entorno (nunca un literal repartido), parar las vistas previas que ya no se usan |
| `actualizar` | El flujo de `actualizar.md` de este kit, entero, adaptado: repositorio, versión instalada (de `<CONFIG>/instalacion-claudio.md`), adaptador en uso y carpeta donde se descarga. Es como el asistente busca y aplica versiones nuevas |

## Lo que ya existe

Agentes y skills con el mismo nombre: **no los sobrescribas**. Compáralos con esta descripción y,
si les falta algo importante, propónmelo como diff aparte. Los que no están en esta lista son
trabajo del usuario: no los toques ni los "normalices".

## Criterios de aceptación

- Las cuatro carpetas y `INDICE.md` existen; el número de notas previas no ha cambiado.
- `explorador` y `verificador` existen con el formato del adaptador y sin capacidad de lanzar agentes
  (o, sin subagentes, la sección de delegación aplica el plan B).
- Las tres skills existen y una sesión nueva en modo no interactivo, preguntada por qué skills tiene
  para la memoria y para actualizar, las nombra (orden en el adaptador).
