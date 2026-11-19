# M5 — Auditoría, optimización y puesta a punto del sistema

Para: todos los perfiles. En `terminal`, solo el inventario de solo lectura y las recomendaciones; sin cambios del sistema.

## Por qué

Un asistente que va a trabajar en la máquina necesita saber en qué estado está: si el SO tiene soporte,
qué falla, qué sobra y qué la frena. En un equipo virgen va primero, porque lo que encuentre cambia qué
otros módulos encajan.

## Cómo se trabaja

Como un proyecto: carpeta persistente `auditoria-sistema/` en la carpeta de proyectos, con `ESTADO.md`,
`DECISIONES.md` y `DESCRIPCION.md` creados desde el inicio, nunca en temporales. Así el trabajo sobrevive
a los reinicios. Si ya existe una auditoría anterior, léela y ofrece revisar solo lo nuevo.

**Equipo gestionado** (la Fase 0 lo detecta: dominio, Intune/MDM, SCCM, perfil de inscripción): lo que
gestiona la organización (sus agentes, servicios, tareas programadas y políticas) se inventaría pero no
se toca ni se propone quitar. Lo que necesite administrador se junta en un solo script con log y marca
de fin, que el usuario ejecuta elevado una vez; si su cuenta no es de administrador, se queda en
informe.

1. **Inventario (solo lectura)**
   - Sistema y paquetes: SO y versión, y si sigue con soporte; actualizaciones y reinicio pendientes;
     repositorios de terceros.
   - Servicios activos y fallidos; tiempo de arranque y qué lo retrasa.
   - Hardware y drivers, verificados por capas: kernel → topología → herramienta de usuario.
   - Disco, swap, memoria y temperaturas.
   - Ante un fallo de hardware, busca la evidencia en el log antes de opinar. Si el sistema arranca
     desde un disco portátil en varios equipos, la falta de uso en uno no vale para otro.
   - **Candidatos a optimizar**:
     - servicios que no encajan con el equipo: módem sin módem, impresión sin impresoras, directorio
       corporativo sin configurar, informes de fallos, agentes de VM en un equipo físico, escritorio
       remoto sin uso;
     - autoarranques de sesión;
     - aplicaciones con y sin rastro de uso;
     - kernels antiguos y cachés;
     - consumo real en reposo.

     De cada candidato, recoge la evidencia de que no se usa.
2. **Informe priorizado**: cada hallazgo con su beneficio, riesgo, reversibilidad y coste. Todavía no se
   cambia nada.
   - Separa lo que **mejora el rendimiento** (servicios y autoarranques) de lo que **solo libera disco**,
     con cifras estimadas.
   - Simula cada desinstalación y descarta lo que arrastre metapaquetes del escritorio o herramientas de
     administración.
   - Antes de proponer software de terceros o de sistema (kernels, drivers), comprueba qué aporta de
     verdad en la versión detectada: simulación de dependencias, versión y fecha del paquete.
   - Si el SO está sin soporte, ofrece la subida de versión.
3. **Elección**: el usuario elige qué aplicar, entre opciones con las recomendadas marcadas. Si el CLI
   admite objetivos de sesión, propón uno con cláusula de espera obligatoria: «todos los cambios elegidos
   aplicados y verificados, o a la espera de una respuesta o un reinicio del usuario».
4. **Aplicación, de uno en uno, cada cambio con su comprobación**:
   - Primero lo reversible:
     - desactivar servicios, no desinstalarlos, guardando antes la lista de los que estaban activados;
     - ocultar autoarranques con una copia;
     - vaciar cachés.
   - Desinstalar o quitar kernels solo tras elegirlo, conservando siempre el kernel anterior.
   - No desactives lo que sirva al dominio del usuario sin preguntar (p. ej. el descubrimiento de red si
     hace domótica).
   - Todo lo que requiera root va en **un** script en la carpeta del proyecto:
     - desacoplado, con `sudo -A` (una sola ventana de contraseña), log y marca de fin;
     - con un script de deshacer para los servicios;
     - apt no interactivo: `DEBIAN_FRONTEND=noninteractive`, entrada desde `/dev/null`, simulación
       previa y `-o DPkg::Lock::Timeout=600`.
   - Antes de subir de versión el SO o el kernel:
     - copia verificada de la carpeta personal y de la configuración del sistema;
     - repositorios de terceros desactivados;
     - menú de arranque visible;
     - el reinicio pendiente, resuelto.
   - Los procesos largos van desacoplados e idempotentes, y se esperan con un monitor.
5. **Medición antes y después**: el arranque se mide en el **segundo** arranque tras un cambio de kernel
   o de SO.
6. **Cierre**:
   - qué mejoró, con cifras;
   - qué quedó sin resolver y su impacto real;
   - qué solo puede confirmar el usuario (táctil, cámara, sonido, suspensión).

   Si un diagnóstico se alarga sin señal clara, para: deja la causa localizada en `ESTADO.md` y en
   `memoria/errores/`, y pregunta si seguir.

## Criterios de aceptación

- El informe existe en la carpeta del proyecto, con evidencia por hallazgo.
- Cada cambio aplicado tiene su comprobación anotada y su forma de deshacerlo.
- Lo que espera reinicio o confirmación física está marcado como tal en `ESTADO.md`.
