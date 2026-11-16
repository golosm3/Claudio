# Fase 2 — Copia de seguridad (obligatoria si no es virgen)

Antes de modificar nada, empaqueta `<INSTRUCCIONES>`, `<AJUSTES>`, `<AGENTES>`,
`<SKILLS>`, `<CONFIG>/memoria/` y `<CONFIG>/hooks/` (lo que exista; no las cachés ni los historiales de
sesión) en `<CONFIG>/backups/prompt-inicial-AAAAMMDD-HHMM.tar.gz`. Verifica el archivo
listando su contenido y comparando el número de ficheros con el origen. Si la copia
falla, para y dímelo: sin copia verificada no se toca nada.

Si la Fase 0 detectó una interfaz instalada o `personalidades/`, la copia también los
incluye (en el mismo archivo o en un segundo `prompt-inicial-proyectos-AAAAMMDD-HHMM.tar.gz`):
la carpeta de la interfaz con su `datos/` (conversaciones y ajustes) pero sin `venv`,
`node_modules` ni `__pycache__`, la carpeta `personalidades/`, y los archivos de servicio y
lanzadores de la interfaz (`~/.config/systemd/user/`, `~/.config/autostart/` y su `.desktop`
en `~/.local/share/applications/`). Se verifica igual: listado y recuento de ficheros.
