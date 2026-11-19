# M1 — Elevación gráfica de privilegios y red de seguridad

Para: perfil `linux`. En `macos` y `windows` no se construye el askpass: documenta en `<INSTRUCCIONES>` el
mecanismo nativo (osascript con privilegios de administrador / UAC con `Start-Process -Verb RunAs` y
espera a que termine) y, si no hay hook que analice la shell, monta la red de seguridad como reglas
`deny` de permisos más una regla de confirmar borrados y formateos. En `terminal` sin sesión gráfica no
hay ventana de contraseña: el usuario ejecuta él mismo lo que pida root.
Necesita: hooks que bloquean (si el programa no los tiene, se instala solo el askpass y las órdenes
vetadas pasan a texto en `<INSTRUCCIONES>`, con el aviso de que nada las hace cumplir).

## Por qué

- El asistente no puede escribir contraseñas, y no debe pedirlas en el chat. Un `sudo` normal se queda
  esperando una contraseña que nunca llega. Con una ventana gráfica, la escribe el usuario.
- Un asistente con shell puede ejecutar por error órdenes irreversibles. Un hook que las bloquea antes de
  que se ejecuten es más fiable que una instrucción en el prompt.

## Qué construir

1. **Askpass** en `<CONFIG>/hooks/`: un programa que muestra una ventana de contraseña con el primer
   diálogo disponible (zenity → kdialog → yad → ssh-askpass). Lleva el título con el nombre del asistente
   y un timeout (variable `ASKPASS_TIMEOUT`, por defecto 120 s) que devuelve un error si nadie responde.
   Se exporta como `SUDO_ASKPASS` en el entorno del asistente (cómo, en el adaptador).
2. **Hook previo a la ejecución de órdenes de shell** (formato y forma de bloquear, en el adaptador;
   con un mensaje que explique el motivo y diga «lánzalo tú con `! …`»).
   - Debe **tokenizar como el shell**, no buscar subcadenas. Una palabra dentro de comillas, de un
     comentario o de un heredoc que no se pasa a un shell es dato, no orden.
   - Debe analizar recursivamente lo que se ejecuta de verdad: `sh/bash -c`, `eval`, `$(…)`, backticks,
     `<(…)`, heredoc a un shell, tuberías, `&&`/`;`. También los envoltorios `env`, `timeout`, `nohup`,
     `xargs`, `nice` y `sudo -A`.
   - Bloquea:
     - **sudo sin `-A`** (permitidos `-A`, `-n`, `-k`, `-K`).
     - **apt/apt-get/aptitude bajo sudo** que instalen o quiten paquetes sin
       `DEBIAN_FRONTEND=noninteractive`, puesto *después* de sudo porque sudo limpia el entorno, y sin
       `-o DPkg::Lock::Timeout=N`. También `dpkg -i/-r/-P/--configure` sin lo primero. Motivo: un aviso
       de debconf deja apt esperando una tecla que nunca llega.
     - **git destructivo**:
       - `reset --hard`;
       - `push --force`/`-f` (se permite `--force-with-lease`);
       - `clean -f/-d/-x`, `branch -D`, `stash drop|clear`;
       - `checkout --` o `-f`;
       - `restore` sin `--staged`.
     - **Borrado masivo**:
       - `rm -r` sobre `/`, `~`, `$HOME`, `/home/<usuario>`, `.`, `*`, las raíces del sistema, o sobre
         `$VAR/` (si la variable está vacía, equivale a `/`);
       - `--no-preserve-root`;
       - `find … -delete` o `-exec rm` sobre esas raíces;
       - `chmod`/`chown -R` sobre ellas.
     - **Disco**:
       - `dd of=/dev/sdX`, `mkfs*`, `wipefs`, `shred`, `blkdiscard`;
       - redirecciones y `tee` a dispositivos de bloque o a `/etc/passwd|shadow|sudoers|fstab|group|hosts`.
     - **Ejecutar lo descargado**: `curl|wget … | sh|bash|python`, `bash <(curl …)`, `sh -c "$(curl …)"`.
     - **Leer credenciales**, con cualquier lector (cat, grep, cp, base64, intérpretes):
       - `.env*`, salvo `.env.example`;
       - `~/.aws`, `~/.gnupg`, `.password-store`, `.netrc`;
       - claves `id_*` y `*.pem|key|p12|pfx|jks`;
       - `.credentials.json` y el `hosts.yml` de gh.
       - Dentro de `~/.ssh` se permiten `config`, `known_hosts`, `authorized_keys` y `*.pub`.
3. **Batería de pruebas** del hook, con casos que deben bloquearse y casos que deben pasar. Los casos que
   deben pasar incluyen las mismas palabras dentro de comillas, comentarios o heredocs de datos, y
   `--force-with-lease`. Hazla amplia (cientos de casos es lo normal). Debe pasar entera **antes** de
   activar el hook.
4. Fusiona en `<AJUSTES>` (Fase 5) la variable `SUDO_ASKPASS` y el registro del hook, con el formato
   del adaptador.

Uso posterior, para tareas de root largas: todo en **un** script lanzado desacoplado, por ejemplo con
`systemd-run --user`, pasando `DISPLAY` y `SUDO_ASKPASS`, con `sudo -A`. Así hay una sola ventana de
contraseña, el script sobrevive a la sesión y deja log con marca de fin.

## Criterios de aceptación

- La batería pasa entera (anota la orden y el recuento).
- En una sesión nueva, pedir `sudo whoami` acaba bloqueado por el hook (visible en la salida de `<NO_INTERACTIVO>`).
- `ASKPASS_TIMEOUT=5 sudo -A true` muestra la ventana y termina con error a los 5 s si nadie responde.
  Que la ventana aparece lo confirma el usuario.
- `git reset --hard` y `cat ~/.ssh/id_ed25519` se bloquean; `echo "git reset --hard"` pasa.
