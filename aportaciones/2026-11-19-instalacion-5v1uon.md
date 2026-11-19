# Retorno — instalacion · kit v3.2 · 2026-11-19

## Sistema

| Dato | Valor |
|---|---|
| SO y versión | Windows 11 Enterprise, build 26300 (equipo gestionado por la organización: MDM/SCCM) |
| Escritorio | Windows (interfaz ClaudioUI) |
| Arquitectura · CPU · núcleos | AMD64 · Intel Core i7-1255U · 10 núcleos / 12 hilos |
| RAM | 32 GB |
| GPU · VRAM | Intel Iris Xe (integrada) · 2 GB compartida |
| Disco libre (aprox.) | ~630 GB |
| Virtualización | WSL disponible |
| Runtimes | PowerShell 5.1, git 2.56, Python 3.13.17 (instalado durante la auditoría por winget, ámbito usuario); sin Node, Docker ni gh |

## Asistente

| Dato | Valor |
|---|---|
| Programa y versión | Claude Code 2.1.328 dentro de ClaudioUI |
| Adaptador | del kit (`claude-code`) |
| Modelos usados en la instalación | Opus 5.5 (hilo principal); Sonnet 5.5 y Haiku 5.5 en pruebas `-p` y subagentes |
| Modo de permisos | auto (clasificador) en la sesión de instalación |

## Resultado

| Fase o módulo | Estado | Motivo si no quedó instalado |
|---|---|---|
| 0 Detectar · 1 Entrevista · 2 Copia | instalado | |
| 3 Instrucciones · 4 Memoria/agentes/skills | instalado | |
| 5 Ajustes (modelo, autocompactado, línea de estado) | instalado; línea de estado sin verificar visualmente | la confirma el usuario abriendo `claude` en una terminal |
| M4 Interfaz | instalado | ya era ClaudioUI |
| M5 Auditoría | instalado; efecto tras reinicio sin verificar | reinicio pendiente |
| M1 Elevación y red de seguridad | instalado (degradado) | sin hook de shell en Windows, solo `permissions.deny` |
| M2 Navegador | instalado | permiso `allow` pendiente de decisión del usuario |
| M3 Personalidades | instalado | valores por defecto (el usuario saltó las preguntas) |
| M6, M7 | omitido | no elegidos |
| M8 Agentes avanzados | instalado | |
| 7 Verificación | hecha | |

Capacidades degradadas y plan B usado:
- M1: en Windows no hay `sudo -A` ni hook de análisis de shell → elevación por UAC (`Start-Process -Verb RunAs` + `WaitForExit`) y red de seguridad solo con `permissions.deny`, más una regla en las instrucciones de confirmar borrados/formateos aunque no estén bloqueados.
- Arranque con el sistema: tarea programada o carpeta de Inicio en vez de systemd.

## Problemas que el kit no tenía previstos

| Archivo y paso del kit | Qué decía | Qué pasó | Cambio propuesto |
|---|---|---|---|
| `modulos/08-agentes-avanzados.md`, criterios de aceptación | Encargo real a `arquitecto-agentes` que crea un skill | Lanzado desde `claude -p`, el subagente no puede escribir en `~/.claude` (ruta protegida); tampoco `implementador` si el plan vive bajo `~/.claude`. Además, la sesión de instalación no ve los agentes creados en ella (el tipo de agente «no existe» hasta una sesión nueva) | Indicar: probar en sesión nueva `-p`; el arquitecto escribe en una carpeta de preparación bajo `<PROYECTOS>` y el hilo principal la copia; los proyectos de prueba van en `<PROYECTOS>`, no en `<CONFIG>` |
| `fases/05` (adaptador claude-code), comprobar `/autocompact` con `-p` | Orden `claude -p /autocompact` | En Git Bash de Windows, `/autocompact` se convierte en una ruta (conversión de rutas MSYS) | En Windows, lanzar desde PowerShell o con `MSYS_NO_PATHCONV=1` |
| Línea de estado (fase 5) en Windows | Script de la plantilla | PowerShell 5.1 lee los `.ps1` sin BOM como ANSI y rompe `▮▯·⚠`; el arnés de pruebas también necesita `[Console]::OutputEncoding = UTF8` | Plantilla de Windows: `.ps1` con BOM UTF-8 y pruebas que fijen la codificación de salida |
| M2 navegador, lanzador en Windows | Lanzador de shell | Hace falta también un `.cmd` para PowerShell; PS 5.1 elimina las comillas dobles internas al pasar argumentos a un `.cmd` (afecta a `evaluar` con JS) | Documentar: comillas simples dentro del JS en PS 5.1 |
| M2, prueba de uso por sesión nueva | Una sesión nueva usa `navegador` sola | La eligió, pero en `-p` la bloquea el permiso; añadir `permissions.allow` lo denegó el clasificador del modo auto | Proponer la regla `allow` al usuario para que la añada él, en vez de escribirla el asistente |
| M5 auditoría en equipo gestionado | Auditar y proponer cambios | Equipo con MDM/SCCM y servicios corporativos; varias tareas programadas de terceros solo se desactivan como administrador | Avisar de no tocar servicios corporativos en equipos gestionados y agrupar lo que requiere elevación en un único script con log y marca de fin |
| ClaudioUI, botón «Instalar Claudio» | — | El usuario temió que instalara dos veces o machacara su configuración | Explicar en la propia interfaz que instala el kit (no la app), que fusiona con copia previa y que desaparece al existir la marca de versión |

## Anonimización

Sustituciones hechas: 0 en el texto final (redactado ya sin nombres, rutas personales, correos ni IPs; comprobado con búsqueda de texto).
