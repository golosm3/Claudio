# Adaptador — opencode

Estado: **sin probar** con el kit. Tabla rellenada desde la documentación oficial
(`https://opencode.ai/docs/`: rules, config, agents, skills, permissions, plugins, commands). Lo marcado
«sin confirmar» no está en la documentación: compruébalo en la máquina (`opencode --help`, su carpeta)
antes de usarlo, y anota el resultado en el progreso.

## Detección

Lo que sepas de ti mismo, o `opencode` en el árbol de procesos de tu shell. Variables propias: sin
confirmar.

## Atención: lee también la configuración de Claude Code

opencode, si no encuentra la suya, carga `~/.claude/CLAUDE.md` y las skills de `~/.claude/skills/`
(se desactiva con `OPENCODE_DISABLE_CLAUDE_CODE`, `…_PROMPT` o `…_SKILLS`). Si en la máquina ya hay una
instalación del kit para Claude Code, decide en la Fase 0 con el usuario:
- **compartir**: opencode usa la misma identidad y memoria (no crees `AGENTS.md` propio; las skills se
  cargan de `~/.claude/skills/`);
- **separar**: crea `~/.config/opencode/AGENTS.md` y pon las variables anteriores en su entorno, para
  que no se carguen las dos veces.

## Términos

| Término | Valor |
|---|---|
| `<CLI>` | `opencode` |
| `<CONFIG>` | `~/.config/opencode` |
| `<INSTRUCCIONES>` | `~/.config/opencode/AGENTS.md` |
| `<INSTR_LOCAL>` | `AGENTS.md` (si no existe, lee `CLAUDE.md`) |
| `<AJUSTES>` | `~/.config/opencode/opencode.json` (o `.jsonc`) |
| `<AGENTES>` | `~/.config/opencode/agents/<nombre>.md` |
| `<SKILLS>` | `~/.config/opencode/skills/<nombre>/SKILL.md` (también lee `~/.agents/skills/`) |
| `<NO_INTERACTIVO>` | `opencode run "<encargo>"`; formato de eventos JSON sin confirmar |
| `<SESIONES>` | sin confirmar |

## Capacidades

| Capacidad | Estado | Cómo |
|---|---|---|
| Instrucciones globales | sí | `<INSTRUCCIONES>` |
| Subagentes | sí | `.md` con frontmatter `description`, `mode: subagent`, `model`, `permissions` |
| Skills | sí | Carpeta con `SKILL.md` (`name`, `description`) |
| Hooks que bloquean | parcial | Reglas de `permissions` en `<AJUSTES>` (`allow`/`ask`/`deny` por herramienta y patrón) o un plugin que deniega en la evaluación de permisos. Sirve para la red de seguridad de M1 si la lista se expresa como reglas `deny` |
| Línea de estado | no | Solo un pie configurable; plan B |
| Compactado | sí | `compaction` en `<AJUSTES>` (`auto`, tokens a conservar, margen) |
| Modelo por nivel | sí | `"model": "proveedor/modelo"` global y por agente. Los niveles los elige el usuario según su proveedor (también modelos locales: Ollama, LM Studio, vLLM) |
| No interactivo trazable | parcial | `opencode run` existe; salida estructurada sin confirmar. Si no la hay, verificación manual |
| Canal de permisos | sin confirmar | M4 no se ofrece mientras no se compruebe |

## Órdenes de verificación

Las de las fases, con `<NO_INTERACTIVO>` si da salida legible; si no, verificación manual (el usuario
abre `opencode`, hace la pregunta y pega la respuesta).
