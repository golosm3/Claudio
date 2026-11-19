# M6 — Permisos ampliados (equipos de reparación)

Para: todos los perfiles.

Solo si el usuario lo elige. **Nunca se recomienda** para el equipo de uso diario.
Necesita: permisos configurables en `<AJUSTES>` (para los modos `asistente` y `bypass`; el modo
`sudo` no depende del programa).

## Por qué

En un equipo dedicado a auditar, reparar o reparticionar otros (p. ej. una USB de arranque con
persistencia), confirmar cada orden de disco estorba. El usuario decide cuánta confirmación quitar.
El asistente instala la herramienta, pero **nunca la ejecuta**.

## Qué construir

Un script `permisos/perm.sh` en la carpeta de proyectos, ejecutable. Con doble clic o sin argumentos
abre un menú en la terminal disponible. Modos:

| Modo | Qué cambia |
|---|---|
| `asistente` | Añade a la lista de permitidos del programa (archivo y formato en el adaptador; en Claude Code, `settings.local.json` → `permissions.allow`) las herramientas de disco (parted, sfdisk, ntfsresize, mount, grub-install, cryptsetup…) con y sin `sudo -A`, y `sudo -A` |
| `bypass` | El modo sin confirmaciones del programa (en Claude Code, `permissions.defaultMode = bypassPermissions`) |
| `sudo` | `/etc/sudoers.d/90-<asistente>-nopasswd` con `NOPASSWD: ALL`, validado con `visudo -c` antes de instalarlo y comprobado con `sudo -k; sudo -n true` |
| `todo` | `asistente` + `sudo` |
| `quitar` | Quita solo lo que añadió el script (las reglas ajenas se conservan), el `bypassPermissions` el modo sin confirmaciones y el archivo de sudoers |

- Antes de tocar el archivo de permisos: copia verificada y diff a la vista. Es idempotente.
- El nombre del archivo de sudoers se sanea: sin acentos ni espacios.
- Avisa tras aplicar: hay que abrir una sesión nueva para que relea la configuración. Con M1, el hook
  sigue exigiendo `sudo -A`. Una interfaz que lance el CLI con un modo de permisos explícito manda sobre
  `bypass`.

## Al instalarlo

- Explica al usuario cómo usarlo y el riesgo: cualquier modo equivale en la práctica a root sin
  confirmación para todo lo que corra como este usuario, subagentes incluidos.
- Anótalo en `## Módulos instalados` con esa advertencia y con `perm.sh quitar` como forma de deshacerlo.

## Criterios de aceptación

Sobre una **copia** del archivo de permisos (variable o argumento de ruta de prueba), nunca sobre la
real:
- `asistente` y luego `quitar` dejan el archivo igual que estaba, incluidas las claves ajenas;
- repetir `asistente` no duplica reglas;
- `visudo -c` rechaza una línea rota antes de instalarla.

El modo `sudo` no se prueba de extremo a extremo: lo ejecuta el usuario.
