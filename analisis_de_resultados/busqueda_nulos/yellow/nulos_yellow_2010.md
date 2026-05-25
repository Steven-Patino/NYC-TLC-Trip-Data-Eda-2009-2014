# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Yellow Taxi 2010

Este documento contiene el diagnóstico técnico de integridad y completitud estructural del conjunto de datos de **Yellow Taxi** correspondiente al año **2010**. El análisis abarca un volumen histórico total de **169,001,162 registros** procesados mediante DuckDB directamente desde el lago de datos en AWS S3, aplicando resolución de conflictos de esquemas nativos (`union_by_name`).

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero detalla el comportamiento de las 18 columnas analíticas del proyecto más la columna técnica corrupta detectada en el almacenamiento físico para el año 2010:

| # | Nombre de la Columna | Registros Nulos | Porcentaje de Nulos | Estado Analítico / Diagnóstico |
| :-: | :--- | :-: | :-: | :--- |
| **1** | `vendor_id` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **2** | `pickup_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **3** | `dropoff_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **4** | `passenger_count` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **5** | `trip_distance` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **6** | `pickup_longitude` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **7** | `pickup_latitude` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **8** | `payment_type` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **9** | `fare_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **10** | `tip_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **11** | `tolls_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **12** | `total_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **13** | `surcharge` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **14** | `mta_tax` | 0 | 0.0% | 🟢 Completitud Perfecta (Estandarizada) |
| **15** | `rate_code` | 0 | 0.0% | 🟢 Completitud Perfecta (Estandarizada) |
| **16** | `dropoff_longitude` | 110 | **0.0001%** | 🟡 Anomalía de Captura GPS Menor |
| **17** | `dropoff_latitude` | 110 | **0.0001%** | 🟡 Anomalía de Captura GPS Menor |
| **18** | `congestion_surcharge` | 169,001,162 | **100.0%** | 🔴 Ausente por Era Temporal |
| **--** | `__index_level_0__` | 144,971,391 | **85.7813%** | 🚨 Columna Técnica Corrupta |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico temporal

### 🟢 2.1. Estandarización de Variables de Negocio
A diferencia del año anterior (2009), la TLC logró capturar de forma mandatoria las columnas `rate_code` y `mta_tax`, alcanzando un **100% de completitud**. La consulta ejecutó exitosamente el casteo de `rate_code` absorbiendo la mutación transaccional interna (transición de tipo de dato de `BYTE_ARRAY` a `INT64`).

### 🟡 2.2. Pérdidas en Destinos Geoespaciales
Se detectaron **110 registros** con valores nulos en `dropoff_longitude` y `dropoff_latitude`. Aunque representan apenas un $0.0001\%$ del dataset, denotan fallas de hardware en los taxímetros en el momento de cerrar la carrera. 
* **Acción sugerida:** Al ser un volumen insignificante, estas filas pueden ser descartadas en la capa de transformación (`trusted`) si el caso de uso requiere analítica geoespacial estricta.

### 🚨 2.3. Confirmación de Contaminación por Índices (`__index_level_0__`)
Se confirma la existencia de la columna basura `__index_level_0__` afectando al **85.7813%** del dataset de este año. Esto demuestra que los archivos del 14.22% restante arrastran un índice de persistencia inútil para el negocio.
* **Acción obligatoria:** Debe excluirse explícitamente en el `SELECT` final de la transformación hacia la capa limpia para ahorrar almacenamiento y evitar transferencias de red innecesarias.