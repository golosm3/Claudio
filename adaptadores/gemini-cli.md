# Adaptador — Gemini CLI

Estado: **sin probar** con el kit. Tabla rellenada desde la documentación oficial
(`https://geminicli.com/docs/`: configuration, GEMINI.md, subagents, skills, hooks, headless).
Lo marcado «sin confirmar» compruébalo en la máquina (`gemini --help`, `~/.gemini/`) antes de usarlo.

Aviso: según su propia documentación, desde junio de 2026 Gemini CLI se sustituye por Antigravity CLI
para las cuentas gratuitas y de Google One. Si el usuario tiene Antigravity, usa el adaptador genérico
y no supongas que estas rutas valen.

## Detección

Lo que sepas de ti mismo, o `gemini` en el árbol de procesos de tu shell. Variables propias: sin
confirmar.

## Términos

| Término | Valor |
|---|---|
| `<CLI>` | `gemini` (npm `@google/gemini-cli`, Node ≥ 20) |
| `<CONFIG>` | `~/.gemini` |
| `<INSTRUCCIONES>` | `~/.gemini/GEMINI.md` (el nombre se cambia con `context.fileName`, p. ej. a `AGENTS.md`) |
| `<INSTR_LOCAL>` | `GEMINI.md` (o el nombre de `context.fileName`) |
| `<AJUSTES>` | `~/.gemini/settings.json` |
| `<AGENTES>` | `~/.gemini/agents/<nombre>.md` |
| `<SKILLS>` | `~/.gemini/skills/<nombre>/SKILL.md` (también `~/.agents/skills/`) |
| `<NO_INTERACTIVO>` | `gemini -p "<encargo>" -o stream-json` |
| `<SESIONES>` | sin confirmar |

## Capacidades

| Capacidad | Estado | Cómo |
|---|---|---|
| Instrucciones globales | sí | `<INSTRUCCIONES>` |
| Subagentes | sí | `.md` con frontmatter `name`, `description`, `kind`, `tools`, `model`, `max_turns`. Para que no lance otros, no le des la herramienta de agentes en `tools` |
| Skills | sí | Carpeta con `SKILL.md` |
| Hooks que bloquean | sí | `hooks.BeforeTool` en `<AJUSTES>` con `matcher`; bloquea con código de salida 2 o JSON `"decision": "deny"` |
| Línea de estado | no | Plan B |
| Compactado | sí | `model.compressionThreshold` (fracción del contexto, por defecto 0.5) |
| Modelo por nivel | parcial | `model.name` global y `model` por agente; los niveles los elige el usuario entre los modelos de su cuenta. Sin modo «potente al planificar» documentado |
| No interactivo trazable | sí | `-o stream-json`: eventos `init`, `message`, `tool_use`, `tool_result`, `error`, `result` |
| Canal de permisos | sin confirmar | Hay modos de aprobación (`--approval-mode default\|auto_edit\|yolo\|plan`), pero no un canal para responder permisos desde otro programa. M4 no se ofrece mientras no se compruebe |
| Permisos | sí | `general.defaultApprovalMode` en `<AJUSTES>` |

## Órdenes de verificación

Las de las fases con `<NO_INTERACTIVO>`; la delegación se ve como `tool_use` hacia el agente, y el hook
de M1 como denegación en el stream.
