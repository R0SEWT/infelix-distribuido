# aire-distribuido

Monitoreo de la calidad del aire en el Perú con las estaciones de SENAMHI y OEFA: carga concurrente
de millones de lecturas, K-Means paralelo en un clúster de nodos Go que agrupa estaciones y horas en
regímenes de contaminación por PM2.5/PM10, API coordinadora y un panel web de alertas tempranas de
salud respiratoria.

Trabajo Final del curso Programación Concurrente y Distribuida (1ACC0065), UPC, 2026-20. Caso 10 del
enunciado: Monitoreo Calidad del Aire y Sensores Ambientales.

> Las estaciones no cubren el territorio de forma pareja. Una zona sin estación no es una zona con
> aire limpio; el panel y el informe lo dicen explícitamente.

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

Los datos no se versionan: el CSV de OEFA pesa 1.3 GB. Ver `data/README.md` para obtenerlos.

## Flujo de trabajo

Git Flow: `develop` es la rama por defecto. Cada cambio va en una rama `feature/<tema>` con PR a
`develop`. Las entregas salen de `release/<pc3|pc4|tb2>` hacia `main`.
