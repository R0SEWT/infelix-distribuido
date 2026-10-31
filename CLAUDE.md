# aire-distribuido — AI Agent Instructions

Trabajo Final (TB2) del curso **Programación Concurrente y Distribuida (1ACC0065)**, UPC, 2026-20.
Lo trabajan Rody Vilchez, Dayana Gómez y Julio Meza. El cuaderno del curso (apuntes, labs, beads de
las evaluaciones, enunciado) vive en `../concurrente`; este repo es solo el sistema.

## Contexto

- **Problema**: caso 10 del enunciado, Monitoreo Calidad del Aire y Sensores Ambientales: clustering
  y predicción masiva de concentraciones de PM2.5/PM10 para alertas tempranas de salud respiratoria.
  El equipo empezó con el caso 2 (denuncias SIDPOL) y el profesor lo reasignó al 10; el repo se llamaba
  `infelix-distribuido`.
- **Pregunta de impacto social** (borrador, falta validarla con el equipo): ¿en qué estaciones y a qué
  horas se concentran los episodios de PM2.5/PM10 que superan el ECA de aire, y con cuánta anticipación
  se podría avisar a la población cercana?
- **Cobertura de estaciones**: las estaciones no cubren el territorio de forma pareja. Una zona sin
  estación no es una zona con aire limpio; cualquier mapa de "zonas de riesgo" tiene que decirlo. Es
  el ángulo ético que pide el DD (ABET SO4).
- **Procedencia de datos** (detalle y enlaces en `data/README.md`):
  - OEFA, Vigilancia y Seguimiento ambiental en la calidad del aire: 34 estaciones, CSV de 1.3 GB.
    Es la fuente principal y la que pasa el millón de filas que pide el enunciado.
  - SENAMHI, Monitoreo de los contaminantes del aire en Lima Metropolitana: 577,794 registros.
  - Ninguna se versiona, por tamaño. Lo que entra al repo es una muestra pequeña en `data/sample/`.
- **Algoritmo**: K-Means paralelo. Cada nodo asigna su partición a los centroides y devuelve sumas y
  conteos parciales por cluster; el coordinador recalcula los centroides y repite hasta converger. Una
  lectura nueva se clasifica por su centroide más cercano, y la alerta sale del régimen más los
  umbrales del ECA (DS 003-2017-MINAM).
- **Propuesta técnica**: `docs/propuesta.md`. Sale del borrador SENAMHI de setiembre
  (`../concurrente`, rama `feat/senamhi-ods13`), con el modelo cambiado de árbol de decisión a K-Means.

## Arquitectura

Las cuatro partes del enunciado, una por entregable:

| Parte | Qué hace | Entregable |
|------|---------|-----------|
| A — Cargador concurrente | lee el CSV de OEFA por bloques con goroutines y channels, limpia y valida | PC3 (07/11) |
| B — K-Means distribuido | nodos con sumas parciales por cluster, el coordinador recalcula centroides por TCP | PC3 diseño, PC4 clúster |
| C — API coordinadora | REST/WebSocket en Go, <100 ms, MongoDB + Redis | PC4 (21/11) |
| D — Frontend SPA | mapa de estaciones con su régimen y las alertas en tiempo real | TB2 (29/11) |

Todo en Go, desplegado con Docker Compose (UI, API, nodos ML, Mongo, Redis).

```bash
go test -race ./...     # siempre con -race: sin él un test no demuestra nada sobre concurrencia
```

## Archivos clave

| Archivo | Propósito |
|------|---------|
| `go.mod` | Módulo `github.com/R0SEWT/aire-distribuido`. |
| `docs/propuesta.md` | Propuesta técnica: datos, K-Means distribuido, sincronización y experimentos. |
| `data/README.md` | Fuentes, volumen y enlaces de descarga. |
| `.github/workflows/go.yml` | CI: `go test -race`, `go vet`, `gofmt`. |
| `../concurrente/materials/extra/CC65-TB2-1ACC0065_202620.pdf` | Enunciado y rúbrica (no versionado: es del profesor). |

## Convenciones

- **Git Flow**: `develop` es la rama por defecto; trabajo en `feature/*` → PR a `develop`; cada
  entrega sale de `release/pc3`, `release/pc4`, `release/tb2` → PR a `main`. Nunca abrir una PR con
  head=`develop`: `delete_branch_on_merge` la borraría (pasó en `concurrente`, PR #40). Ese ajuste
  está apagado aquí, pero la regla sigue.
- **Issues de GitHub solo para problemas** (bugs, bloqueos). El trabajo se sigue en beads. El tablero
  Kanban de GitHub se arma al final, antes de cada entrega.
- **Informe en Word**, nombre `1ACC0065_PC3_202610_Informe_UXXXXXXXX` (el enunciado dice 202610).
- **Commits, PRs e issues en español**, sin metadata de IA.
- Equipo: si Dayana y Julio se suben, se reparte; si no, el trabajo avanza igual. Nada del flujo
  debe depender de que ellos usen beads o Claude.

<!-- BEGIN BEADS INTEGRATION v:1 profile:minimal hash:6cd5cc61 -->
## Beads Issue Tracker

This project uses **bd (beads)** for issue tracking. Run `bd prime` to see full workflow context and commands.

### Quick Reference

```bash
bd ready              # Find available work
bd show <id>          # View issue details
bd update <id> --claim  # Claim work
bd close <id>         # Complete work
```

### Rules

- Use `bd` for ALL task tracking — do NOT use TodoWrite, TaskCreate, or markdown TODO lists
- Run `bd prime` for detailed command reference and session close protocol
- Use `bd remember` for persistent knowledge — do NOT use MEMORY.md files

**Architecture in one line:** issues live in a local Dolt DB; sync uses `refs/dolt/data` on your git remote; `.beads/issues.jsonl` is a passive export. See https://github.com/gastownhall/beads/blob/main/docs/SYNC_CONCEPTS.md for details and anti-patterns.

## Agent Context Profiles

The managed Beads block is task-tracking guidance, not permission to override repository, user, or orchestrator instructions.

- **Conservative (default)**: Use `bd` for task tracking. Do not run git commits, git pushes, or Dolt remote sync unless explicitly asked. At handoff, report changed files, validation, and suggested next commands.
- **Minimal**: Keep tool instruction files as pointers to `bd prime`; use the same conservative git policy unless active instructions say otherwise.
- **Team-maintainer**: Only when the repository explicitly opts in, agents may close beads, run quality gates, commit, and push as part of session close. A current "do not commit" or "do not push" instruction still wins.

## Session Completion

This protocol applies when ending a Beads implementation workflow. It is subordinate to explicit user, repository, and orchestrator instructions.

1. **File issues for remaining work** - Create beads for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **Handle git/sync by active profile**:
   ```bash
   # Conservative/minimal/default: report status and proposed commands; wait for approval.
   git status

   # Team-maintainer opt-in only, unless current instructions forbid it:
   git pull --rebase
   git push
   git status
   ```
5. **Hand off** - Summarize changes, validation, issue status, and any blocked sync/commit/push step

**Critical rules:**
- Explicit user or orchestrator instructions override this Beads block.
- Do not commit or push without clear authority from the active profile or the current user request.
- If a required sync or push is blocked, stop and report the exact command and error.
<!-- END BEADS INTEGRATION -->
