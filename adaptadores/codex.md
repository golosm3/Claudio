# Adaptador — Codex CLI

Estado: **sin probar** con el kit. Tabla rellenada desde la documentación oficial de Codex (OpenAI;
`https://developers.openai.com/codex/`, que redirige a `learn.chatgpt.com/codex`: AGENTS.md, config, subagents, skills, hooks, non-interactive).
Lo marcado «sin confirmar» compruébalo en la máquina (`codex --help`, `~/.codex/`) antes de usarlo.

## Detección

Lo que sepas de ti mismo, o `codex` en el árbol de procesos de tu shell. Variables propias: `CODEX_HOME`
si está definida (cambia `<CONFIG>`); otras, sin confirmar.

## Términos

| Término | Valor |
|---|---|
| `<CLI>` | `codex` (npm `@openai/codex` o Homebrew) |
| `<CONFIG>` | `~/.codex` (o `$CODEX_HOME`) |
| `<INSTRUCCIONES>` | `~/.codex/AGENTS.md` (`AGENTS.override.md` tiene prioridad si existe) |
| `<INSTR_LOCAL>` | `AGENTS.md` |
| `<AJUSTES>` | `~/.codex/config.toml` (TOML) |
| `<AGENTES>` | `~/.codex/agents/<nombre>.toml` |
| `<SKILLS>` | carpeta con `SKILL.md` bajo `<CONFIG>`; ruta exacta sin confirmar |
| `<NO_INTERACTIVO>` | `codex exec --json "<encargo>"` |
| `<SESIONES>` | sin confirmar |

## Capacidades

| Capacidad | Estado | Cómo |
|---|---|---|
| Instrucciones globales | sí | `<INSTRUCCIONES>` |
| Subagentes | sí | TOML con `name`, `description`, `developer_instructions`, `model`, `model_reasoning_effort` |
| Skills | sí | `SKILL.md` con `name` y `description` |
| Hooks que bloquean | sí | `PreToolUse` en `hooks.json` o en `[hooks]` de `<AJUSTES>`; deniega con `permissionDecision: "deny"` o código 2. El usuario debe aprobar los hooks una vez con `/hooks` |
| Línea de estado | sin confirmar | Plan B mientras no se compruebe |
| Compactado | sin confirmar | Posible clave `model_auto_compact_token_limit`; si no existe, se omite |
| Modelo por nivel | parcial | `model = "…"` global, perfiles y `model` por agente. Sin modo «potente al planificar» documentado |
| No interactivo trazable | sí | `codex exec --json`: eventos JSON por línea |
| Canal de permisos | sin confirmar | Hay `approval_policy` y `sandbox_mode`, pero no un canal documentado para responder permisos desde otro programa. M4 no se ofrece mientras no se compruebe |
| Permisos | sí | `approval_policy`, `sandbox_mode` en `<AJUSTES>` |

## Órdenes de verificación

Las de las fases con `<NO_INTERACTIVO>`. Para el hook de M1, primero hay que aprobarlo con `/hooks` en
una sesión interactiva; sin eso no se ejecuta.
