# infelix-distribuido

Clasificación distribuida del tipo de delito sobre las denuncias policiales del Perú (SIDPOL,
MININTER/PNP): carga concurrente de más de 1M de registros, Random Forest entrenado en un clúster de
nodos Go, API coordinadora y un panel web de impacto social.

Trabajo Final del curso Programación Concurrente y Distribuida (1ACC0065), UPC, 2026-20.

> El dato registra **dónde se denuncia**, no dónde ocurre el delito. Las zonas que el modelo marca
> como de mayor riesgo heredan ese sesgo; el informe lo trata explícitamente.

## Equipo

- Rody Vilchez
- Dayana Gómez
- Julio Meza

## Cómo correrlo

```bash
go test -race ./...
```

El despliegue con Docker Compose (API, nodos ML, MongoDB, Redis, frontend) llega con la PC4.

## Datos

Los datos no se versionan: el repo es público y los registros incluyen direcciones. Ver
`data/README.md` para obtenerlos.

## Flujo de trabajo

Git Flow: `develop` es la rama por defecto. Cada cambio va en una rama `feature/<tema>` con PR a
`develop`. Las entregas salen de `release/<pc3|pc4|tb2>` hacia `main`.
