# Datos

Nada de esta carpeta se versiona, salvo este README y `sample/`.

| Fuente | Qué es | Dónde |
|--------|--------|-------|
| Portal de Datos Abiertos del Perú | Denuncias policiales registradas (MININTER/PNP). Es la fuente que exige el enunciado. | datosabiertos.gob.pe (pendiente: URL exacta y descarga reproducible) |
| API de MININTER (SIDPOL) | Lima: 3,136,427 filas; nacional: 9,398,491. Parquet. | fuera del repo; se compara contra la del portal |

`sample/` lleva una muestra pequeña para las pruebas y el CI, **sin** columnas de dirección
(`direccion_hecho`) ni observaciones.
