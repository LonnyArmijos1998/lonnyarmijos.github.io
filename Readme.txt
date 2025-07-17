# 📊 Análisis de Datos con BigQuery SQL

Este documento contiene el análisis realizado con consultas SQL en Google BigQuery sobre el dataset `nueva_tabla_union`, que registra viajes de bicicletas públicas con detalles de duración, fechas y tipo de usuario.

---

## 🔍 Dataset

- Proyecto: `ageless-talent-464602-m8`
- Dataset: `lonnyprojectgoogle`
- Tabla: `nueva_tabla_union`

Contiene registros con columnas como: `member_casual`, `started_at`, `ended_at`, entre otros.

---



### 1. Average ride duration by day of the week and user type

```sql
SELECT
  member_casual,
  EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week,
  AVG(ride_length_seconds) AS avg_ride_length_sec,
  CONCAT(
    LPAD(CAST(FLOOR(AVG(ride_length_seconds) / 3600) AS STRING), 2, '0'), ':',
    LPAD(CAST(FLOOR(MOD(CAST(AVG(ride_length_seconds) AS INT64), 3600) / 60) AS STRING), 2, '0'), ':',
    LPAD(CAST(MOD(CAST(AVG(ride_length_seconds) AS INT64), 60) AS STRING), 2, '0')
  ) AS avg_ride_length_hhmmss
FROM (
  SELECT
    member_casual,
    started_at,
    TIMESTAMP_DIFF(ended_at, started_at, SECOND) AS ride_length_seconds
  FROM
    `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`
)
GROUP BY
  member_casual, day_of_week
ORDER BY
  member_casual, day_of_week;



2. Number of rides by day of the week and user type

SELECT
  member_casual,
  EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week,
  COUNT(*) AS number_of_rides
FROM
  `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`
GROUP BY
  member_casual, day_of_week
ORDER BY
  member_casual, day_of_week;


3. Day with highest ride frequency (Mode)

SELECT
  day_of_week,
  COUNT(*) AS frequency
FROM (
  SELECT
    EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week
  FROM
    `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`
)
GROUP BY
  day_of_week
ORDER BY
  frequency DESC
LIMIT 1;


4. Average ride duration by user type

SELECT
  member_casual,
  AVG(ride_length_seconds) AS avg_ride_length_sec,
  CONCAT(
    LPAD(CAST(FLOOR(AVG(ride_length_seconds) / 3600) AS STRING), 2, '0'), ':',
    LPAD(CAST(FLOOR(MOD(CAST(AVG(ride_length_seconds) AS INT64), 3600) / 60) AS STRING), 2, '0'), ':',
    LPAD(CAST(MOD(CAST(AVG(ride_length_seconds) AS INT64), 60) AS STRING), 2, '0')
  ) AS avg_ride_length_hhmmss
FROM (
  SELECT
    member_casual,
    TIMESTAMP_DIFF(ended_at, started_at, SECOND) AS ride_length_seconds
  FROM
    `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`
)
GROUP BY
  member_casual
ORDER BY
  member_casual;


5. Ride duration details in HH:MM:SS format and day of the week

SELECT
  *,
  TIMESTAMP_DIFF(ended_at, started_at, SECOND) AS ride_length_seconds,
  CONCAT(
    LPAD(CAST(FLOOR(TIMESTAMP_DIFF(ended_at, started_at, SECOND) / 3600) AS STRING), 2, '0'), ':',
    LPAD(CAST(FLOOR(MOD(TIMESTAMP_DIFF(ended_at, started_at, SECOND), 3600) / 60) AS STRING), 2, '0'), ':',
    LPAD(CAST(MOD(TIMESTAMP_DIFF(ended_at, started_at, SECOND), 60) AS STRING), 2, '0')
  ) AS ride_length_formatted,
  EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week
FROM
  `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`;



6. Average and maximum ride durations
SELECT
  AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS avg_ride_length_seconds,
  CONCAT(
    LPAD(CAST(FLOOR(AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) / 3600) AS STRING), 2, '0'), ':',
    LPAD(CAST(FLOOR(MOD(CAST(AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS INT64), 3600) / 60) AS STRING), 2, '0'), ':',
    LPAD(CAST(MOD(CAST(AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS INT64), 60) AS STRING), 2, '0')
  ) AS avg_ride_length_formatted,
  MAX(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS max_ride_length_seconds,
  CONCAT(
    LPAD(CAST(FLOOR(MAX(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) / 3600) AS STRING), 2, '0'), ':',
    LPAD(CAST(FLOOR(MOD(MAX(TIMESTAMP_DIFF(ended_at, started_at, SECOND)), 3600) / 60) AS STRING), 2, '0'), ':',
    LPAD(CAST(MOD(MAX(TIMESTAMP_DIFF(ended_at, started_at, SECOND)), 60) AS STRING), 2, '0')
  ) AS max_ride_length_formatted
FROM
  `ageless-talent-464602-m8.lonnyprojectgoogle.nueva_tabla_union`;


