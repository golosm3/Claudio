# Fase 9 — Informe de retorno (opcional)

Sirve para que el kit mejore con cada instalación: un `.md` con el sistema donde se ha instalado, lo
que ha fallado o no estaba previsto y, en una actualización, lo que el usuario tiene y podría servir a
otros. Se ofrece **una vez**, al terminar la Fase 8 (o el paso 6 de `actualizar.md`); si el usuario dice
que no, no se vuelve a ofrecer en esa instalación.

## 1. Ofrecer

Pregunta con tres opciones:
- **Crear y enviar** (recomendada): se crea, lo revisa y se propone al repositorio.
- **Solo crearlo**: queda en local; puede enviarlo más tarde o por otra vía.
- **No**.

Explica en una línea qué incluye y que **nada sale de la máquina sin que vea el texto final y lo
confirme**.

## 2. Reunir los datos

Usa lo que ya está en `<CONFIG>/instalacion-claudio.md` y en lo hecho en esta sesión; lo que falte, lo
detectas con órdenes de solo lectura. Si ese registro no existe (instalaciones anteriores a la v3), lo
que no puedas comprobar va como «no consta»: no lo reconstruyas de memoria. Rellena `plantillas/retorno.md`:
- **Sistema**: SO y versión, escritorio, arquitectura, modelo de CPU y núcleos, RAM, GPU y VRAM,
  disco libre (redondeado), virtualización disponible, runtimes (Python, Node…) con versión.
- **Asistente**: programa y versión, adaptador usado (del kit o genérico), modelos con los que se ha
  hecho la instalación y el modo de permisos.
- **Resultado**: cada fase y módulo con su estado (instalado, fusionado, omitido, fallido, sin
  verificar) y el motivo si no quedó instalado.
- **Problemas**: lo que falló, cómo se resolvió y, sobre todo, **qué no tenía previsto el kit**:
  archivo y paso del kit, qué decía, qué pasó en realidad y el cambio que propones. Un error que el
  kit ya preveía y se resolvió como dice no es un problema: no lo listes.
- **Capacidades degradadas** y el plan B que se usó.
- **Solo al actualizar**: versión de partida y de llegada; qué apartados se aplicaron y cuáles no (y
  por qué: personalización que choca, módulo no instalado, rechazado por el usuario); y **qué tiene
  este sistema que no está en el kit y podría interesar** (agentes, skills, reglas, hooks, módulos
  propios): qué hace, por qué es útil y cómo se generalizaría. Describe, no pegues contenido privado.
  Si alguno merece compartirse entero, va como receta (punto 3).

## 3. Recetas para compartir (opcional, al instalar o al actualizar)

Algunas recetas o diagnósticos de errores que el usuario ha ido guardando (`memoria/recetas/`,
`memoria/errores/`) resuelven problemas que otros usuarios del kit tendrán. Se pueden adjuntar al
informe:
- **Filtro**: solo las de infraestructura del asistente, de la plataforma o de herramientas genéricas
  (instalar o configurar algo, un fallo del programa o del SO con su causa real). Nada de proyectos,
  clientes, datos o equipos concretos.
- Enséñale la lista de candidatas (título y una línea) y que **marque él cuáles**. Ninguna por defecto.
- Cada elegida se reescribe con `plantillas/receta-aportada.md`: rutas y nombres generalizados
  (`<CONFIG>`, `{{PROYECTOS}}`, `<equipo>`, `<usuario>`), con «Probado en» (SO, versión del
  asistente, fecha) y, si es de un error, la causa real y no solo el parche.
- Pasan por la misma anonimización del punto 4 y el usuario ve **cada una entera** antes del envío.
- Se guardan junto al informe, en `<CONFIG>/kit/retornos/recetas/AAAA-MM-DD-<tema>.md`.

## 4. Anonimizar (obligatorio, antes de enseñarlo)

El repositorio es público. Busca en el texto y sustituye por un genérico (`<usuario>`, `<equipo>`,
`<ruta>`, `<red>`):
- nombres de personas, también el del asistente si es propio; nombre de usuario y del equipo;
- rutas personales (`/home/…`, `C:\Users\…`, unidades montadas con nombre propio);
- correos, IPs, nombres de red o de servidores propios, MAC, números de serie;
- tokens, claves, contraseñas o cualquier cosa con forma de secreto: si aparece uno, **para**, no lo
  sustituyas sin más: avisa al usuario de que lo rote y quítalo con su contexto;
- nombres de proyectos, clientes o empresas.

Hazlo con búsqueda de texto real (los valores que conoces de la máquina: usuario, equipo, `HOME`,
nombre del asistente y del usuario, IPs locales) además de leerlo; anota en el informe cuántas
sustituciones hiciste, sin decir cuáles.

## 5. Guardar y revisar

- Guárdalo en `<CONFIG>/kit/retornos/AAAA-MM-DD-<tipo>.md` (tipo: `instalacion` o `actualizacion`).
- Enséñalo **entero** al usuario. Puede quitar o cambiar lo que quiera; aplica sus cambios y vuelve a
  enseñar lo que cambió.
- Con «solo crearlo», termina aquí diciendo la ruta.

## 6. Enviar

Un usuario cualquiera no puede escribir directamente en el repositorio del kit: lo que se envía es una
**propuesta** (pull request) que el mantenedor revisa y acepta. Antes de enviar, dile que la propuesta
quedará pública y asociada a su cuenta de GitHub, y pide confirmación explícita del envío.

Por orden, según lo que haya:
1. **Con `gh` y sesión iniciada** (`gh auth status`): si su cuenta puede escribir en `golosw/Claudio`
   (es quien lo mantiene), crea la rama en el propio repositorio en vez de bifurcarlo. Si no, bifurca `golosw/Claudio` en su cuenta si no
   lo está, crea una rama `retorno-AAAA-MM-DD-<6 caracteres al azar>`, añade el informe como
   `aportaciones/AAAA-MM-DD-<tipo>-<los mismos 6 caracteres>.md` y cada receta elegida como
   `aportaciones/recetas/AAAA-MM-DD-<los mismos 6 caracteres>-<tema>.md` (nada más) y abre la propuesta
   contra `main` con título `Retorno: <tipo> <versión del kit> en <SO>`. Como cuerpo, el apartado
   «Problemas» y la lista de recetas adjuntas. Trabaja en una carpeta temporal nueva, no dentro de
   ninguna instalación.
2. **Con cuenta de GitHub pero sin `gh`**: ofrece instalar `gh` (repositorio oficial del sistema) y
   que inicie sesión él (`gh auth login`, en una terminal suya: es interactivo); después, el punto 1.
   Si no quiere, abre en su navegador `https://github.com/golosw/Claudio/issues/new` y dile que pegue
   como cuerpo de la incidencia **el informe entero** (no un resumen ni la ruta del archivo, que nadie
   más puede abrir), con el título anterior. Las recetas van a continuación, cada una con su título.
   Si no caben, que abra una incidencia por receta citando la primera.
3. **Sin cuenta**: queda en local; dile la ruta por si quiere enviarlo de otra forma.

Al terminar, enseña el enlace de la propuesta o de la incidencia. Si falla, dilo con el error real; el
archivo local se conserva.

## Criterios de aceptación

- Con «No», no se crea nada.
- El archivo existe en `<CONFIG>/kit/retornos/` y no contiene el usuario, el equipo, la ruta de `HOME`,
  IPs locales ni correos (búsqueda de texto con esos valores: sin coincidencias).
- Nada se ha enviado sin que el usuario haya visto el texto final y confirmado.
- Las recetas adjuntas (si las hay) las eligió el usuario una a una, siguen la plantilla y pasan la
  misma búsqueda de datos personales.
- Si se envió, el enlace abre una propuesta en `golosw/Claudio` que solo añade el informe y las
  recetas elegidas, o una incidencia cuyo cuerpo es el informe entero.
