# Adaptador — Claude Code (recomendado, de referencia)

Estado: **probado** de principio a fin (instalación virgen y sobre una previa) con Claude Code 2.x.
Documentación oficial: `https://docs.claude.com/en/docs/claude-code/` (settings, hooks, sub-agents,
skills, statusline, cli-reference, headless).

## Detección

`AI_AGENT` empieza por `claude-code`, o `CLAUDECODE=1` en la shell del asistente.

## Términos

| Término | Valor |
|---|---|
| `<CLI>` | `claude` |
| `<CONFIG>` | `~/.claude` |
| `<INSTRUCCIONES>` | `~/.claude/CLAUDE.md` |
| `<INSTR_LOCAL>` | `CLAUDE.md` |
| `<AJUSTES>` | `~/.claude/settings.json` (JSON; `settings.local.json` para lo local) |
| `<AGENTES>` | `~/.claude/agents/<nombre>.md` |
| `<SKILLS>` | `~/.claude/skills/<nombre>/SKILL.md` |
| `<NO_INTERACTIVO>` | `claude -p "<encargo>" --output-format stream-json --verbose` |
| `<SESIONES>` | `~/.claude/projects/<ruta de trabajo con / → ->/*.jsonl` |

## Capacidades

| Capacidad | Estado | Cómo |
|---|---|---|
| Instrucciones globales | sí | `<INSTRUCCIONES>`; además el `CLAUDE.md` de la carpeta de trabajo |
| Subagentes | sí | `.md` con frontmatter `name`, `description`, `tools`, `model`, `effort`. Para que un agente no lance otros, no incluyas `Agent` en `tools` |
| Skills | sí | Frontmatter `name` y `description` (la descripción dice **cuándo** usarla, para que se active sola) |
| Hooks que bloquean | sí | `hooks.PreToolUse` en `<AJUSTES>`, matcher `Bash`; el hook recibe el JSON de la llamada por stdin y sale con código 2 para bloquear (stderr = motivo que ve el modelo) |
| Línea de estado | sí | `statusLine: {"type": "command", "command": "<ruta absoluta>"}`; recibe por stdin un JSON de la sesión (modelo, `context_window`, `cost.total_cost_usd`, `rate_limits`, `transcript_path`…) |
| Compactado | sí | `"autoCompactWindow": 150000` en `<AJUSTES>` |
| Modelo por nivel | sí | Niveles: económico = `haiku`, normal = `sonnet`, potente = `opus`. Hilo principal: `"model": "opusplan"` (Opus al planificar, Sonnet al ejecutar). Coste relativo: Fable ≈ 2,5× Opus, Opus ≈ 2× Sonnet; Fable solo si el usuario lo pide en la sesión (`/model fable`) |
| No interactivo trazable | sí | `<NO_INTERACTIVO>`: cada línea es un evento JSON (herramientas, resultados, denegaciones, `total_cost_usd`, respuesta final) |
| Canal de permisos | sí | `claude -p --input-format stream-json --output-format stream-json --verbose --permission-prompt-tool stdio`: cada petición de permiso o pregunta (`AskUserQuestion`) llega como evento y se responde por stdin |
| Permisos | sí | `permissions.allow` / `deny` / `defaultMode` en `<AJUSTES>` o `settings.local.json`; `--permission-mode` en la orden manda sobre ellos |
| Elevación gráfica | sí | `env.SUDO_ASKPASS` en `<AJUSTES>` |

## Interfaz (M4) y modelo propio (M7)

| Para | Opción o variable |
|---|---|
| Eventos de entrada y salida | `claude -p --input-format stream-json --output-format stream-json --verbose` |
| Canal de permisos y preguntas | `--permission-prompt-tool stdio` (también `AskUserQuestion`) |
| Reanudar | `--resume <id de sesión>` |
| Añadir al prompt del sistema | `--append-system-prompt "<texto>"` |
| Acotar herramientas | `--tools Read,Bash,Edit,Write,Grep,Glob,WebFetch`; `--tools ""` = ninguna |
| Endpoint propio | `ANTHROPIC_BASE_URL`, `ANTHROPIC_API_KEY` (ficticia para un servidor local) |
| Carpeta de configuración propia | `CLAUDE_CONFIG_DIR` |
| Tope de salida | `CLAUDE_CODE_MAX_OUTPUT_TOKENS` (sin él, el CLI pide ~32K y pisa el límite del servidor) |
| Cortar la sesión desde un hook | JSON `{"continue": false}` |

## Instalación estable del ejecutable (Fase 6, Linux)

- Mira cómo está instalado (`command -v claude`, `readlink` del ejecutable).
- **Nativo** (`~/.local/bin/claude` enlaza a `~/.local/share/claude/versions/…`): no hagas nada.
- **npm/nvm u otro**: recomienda la instalación nativa (`claude install stable`, canal oficial, unos
  200 MB; pregúntalo una vez). No borres la de npm. Si se prefiere no cambiar, crea en
  `~/.local/bin/claude` un lanzador que busque la versión más reciente instalada (nativa o npm, con o
  sin nvm) y la ejecute sin depender del PATH interactivo, guardando antes lo que hubiera.
- Sin `claude`: descarga el instalador oficial a un archivo, revísalo y ejecútalo (nunca `curl | sh`);
  después hay que iniciar sesión con `claude`.

## Órdenes de verificación

| Criterio | Orden | Se espera |
|---|---|---|
| Skills visibles (Fase 4) | `claude -p "¿Qué skills tienes para la memoria y para actualizar?"` | Las nombra |
| Compactado (Fase 5) | `claude -p /autocompact` | Ventana 150k «from settings» |
| Modelo (Fase 5) | `claude -p "Responde solo: ok" --max-turns 1 --output-format stream-json --verbose` | `message.model` es Sonnet; con `--permission-mode plan`, Opus |
| Línea de estado (Fase 5) | Captura real: una `statusLine` provisional que vuelca stdin a un archivo y un mensaje en `claude` interactivo | El script pasa sus pruebas con ese JSON |
| Delegación (Fase 7) | `<NO_INTERACTIVO>` con un encargo para `explorador` sobre una carpeta dentro de la de trabajo (en `-p` no lee fuera sin permiso) | Evento de herramienta `Agent`/`Task` con `subagent_type: explorador` |
| Hook (M1) | `<NO_INTERACTIVO>` pidiendo `sudo whoami` | Bloqueado por el hook (visible en el stream) |
