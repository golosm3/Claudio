# M2 — Navegador automatizable

Necesita: nada del asistente (es una CLI propia); solo `<INSTR_LOCAL>` para documentarla.

## Por qué

La lectura web integrada del asistente (si la tiene) no ejecuta JavaScript ni hace capturas. Muchas páginas actuales solo muestran su contenido
renderizado. Una CLI genérica, documentada una vez, deja al asistente leer, capturar o sacar a PDF
cualquier página sin improvisar un script cada vez.

## Qué construir

- Una CLI `navegador` en el PATH del usuario, con estos subcomandos:
  - `texto URL`: texto visible;
  - `html URL`: HTML renderizado;
  - `captura URL out.png [--completa]`;
  - `pdf URL out.pdf`;
  - `evaluar URL 'expresión JS'`;
  - `enlaces URL`: `texto<TAB>href`;
  - `abrir URL`: con ventana visible, solo para que el usuario intervenga (login, captcha).
- Opciones comunes: `--selector CSS`, `--espera MS`, `--timeout S`.
- Headless por defecto. Si la carga falla, sale con error y código distinto de 0: nunca inventa el
  contenido. No salta muros de pago, captchas ni bloqueos.
- Tecnología: la más simple con lo que haya instalado, por ejemplo Playwright en un venv si hay Python y no
  Node.
  - Si hay Chrome/Chromium del sistema, úsalo en vez de descargar otro navegador.
  - Si hay que descargar uno, la descarga va desacoplada con log.
- **Sistemas de archivos sin enlaces simbólicos ni permisos** (exFAT, FAT, NTFS):
  - el venv va en la carpeta personal, fuera del proyecto;
  - para copiar, `cp -r --no-preserve=mode,ownership` o `rsync -rt` (`cp -a` y `rsync -a` fallan allí);
  - un repositorio git allí necesita `git config core.filemode false`.
- Documéntala **una sola vez**, en el `<INSTR_LOCAL>` de la carpeta de proyectos (configuración local),
  no en el global: qué es, los subcomandos y las opciones, y cuándo usar WebFetch en su lugar (web
  estática).
  Si el programa no tiene lectura web integrada, `navegador` es la vía para toda página.

## Criterios de aceptación

- `navegador texto https://example.com` devuelve el texto de la página; con una URL inexistente sale con
  error y no imprime contenido.
- `captura` y `pdf` generan archivos válidos (`file` los reconoce).
- Una sesión nueva, a la que solo se le pide el resultado de una página con JavaScript y sin decirle el
  comando, usa `navegador` (visible en la salida de `<NO_INTERACTIVO>`; sin ese modo, el usuario lo
  comprueba en una sesión normal).
