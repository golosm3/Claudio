# Fase 7 — Verificación real (sin esto no está terminado)

Repasa el `MANIFIESTO.md`: cada apartado obligatorio y cada módulo elegido debe tener en
`<CONFIG>/instalacion-claudio.md` la marca `hecho (evidencia: …)` con la orden ejecutada y lo que
devolvió. Lo que no pueda comprobarse aquí (visual, físico, reinicio de sesión) se marca
`sin verificar (lo confirma el usuario: …)`. Lo que el programa no permite se marca
`omitido (el programa no lo permite; plan B: …)`.

Las pruebas de sesión usan `<NO_INTERACTIVO>`, guardando la salida en un archivo (desacoplado, con
marca de fin si puede tardar). De ahí salen las herramientas usadas, lo bloqueado por los hooks, los
permisos denegados, el coste si lo da y la respuesta final. Las órdenes concretas de cada criterio
están en el adaptador. Si el programa no tiene modo no interactivo trazable, pide al usuario que abra
`<CLI>`, haga la pregunta y pegue la respuesta.
Estas pruebas no se omiten: si una orden se deniega o falla, dilo, prueba otra vía (otra carpeta de
trabajo, verificación manual) y, si nada funciona, marca el criterio `sin verificar` con el motivo.

1. **Archivos**: relee lo creado o cambiado. No quedan `{{…}}` ni términos `<…>` del adaptador sin
   sustituir; `<AJUSTES>` es válido.
2. **Nada destruido** (si no era virgen): compara con la copia de la Fase 2. Ninguna nota, agente,
   skill, hook, archivo de la interfaz o de `personalidades/` ha desaparecido ni cambiado sin
   aprobación (sha256 contra la copia). Enséñame el recuento antes/después por carpeta. No se ha
   creado ninguna carpeta de proyectos nueva: todo lo nuevo está bajo la detectada.
3. **Sesión nueva**: preguntada «¿Cómo te llamas, en qué idioma respondes y cuáles son dos de tus
   reglas de oro?», responde con el nombre, el idioma y reglas de `<INSTRUCCIONES>`.
4. **Módulos**: pasa los criterios de aceptación de cada módulo instalado.
5. **Delegación** (si hay subagentes): encarga una tarea pequeña al `explorador` sobre una carpeta
   dentro de la de trabajo y comprueba en la salida que lo invocó.
6. **Accesos directos**: los criterios de la Fase 6.
