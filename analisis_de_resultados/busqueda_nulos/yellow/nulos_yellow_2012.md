# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Yellow Taxi 2012

Este documento contiene el diagnóstico técnico de integridad y completitud estructural del conjunto de datos de **Yellow Taxi** correspondiente al año **2012**. El análisis procesó un volumen histórico total de **171,359,007 registros** extraídos directamente mediante DuckDB desde el lago de datos en AWS S3. 

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero presenta el estado de completitud de las variables analíticas clave para el año 2012:

| # | Nombre de la Columna | Registros Nulos | Porcentaje de Nulos | Estado Analítico / Diagnóstico |
| :-: | :--- | :-: | :-: | :--- |
| **1** | `VendorID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **2** | `tpep_pickup_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **3** | `tpep_dropoff_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **4** | `passenger_count` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **5** | `trip_distance` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **6** | `PULocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas Consolidadas) |
| **7** | `DOLocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas Consolidadas) |
| **8** | `RatecodeID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **9** | `payment_type` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **10** | `fare_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **11** | `extra` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **12** | `mta_tax` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **13** | `tip_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **14** | `tolls_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **15** | `total_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **16** | `improvement_surcharge`| 0 | 0.0% | 🟢 Completitud Perfecta |
| **17** | `store_and_fwd_flag` | 81,704,413 | **47.6803%** | 🟡 Intermitencia Técnica Operativa |
| **18** | `congestion_surcharge` | 171,358,984 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |
| **--** | `airport_fee` | 171,359,007 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico Temporal

### 🟢 2.1. Madurez del Esquema de Zonas
Las variables de localización basadas en IDs (`PULocationID` y `DOLocationID`) sostienen su rendimiento impecable del **0% de nulos**. Esto ratifica que para el año 2012 la operación está totalmente adaptada a la granularidad de zonas de tráfico de la TLC, dejando atrás el uso de coordenadas en el core transaccional.

### 🟡 2.2. Estabilidad en la Persistencia de Conexión (`store_and_fwd_flag`)
La columna presenta un **47.6803% de registros nulos**, un comportamiento muy alineado al año 2011 (49.06%). Esto certifica que el mecanismo de caché en hardware de los taxímetros responde a un comportamiento persistente en la infraestructura física de los vehículos de la ciudad.

### 🔴 2.3. Sostenibilidad de Campos Latentes
A pesar de la evolución del volumen, `congestion_surcharge` y `airport_fee` continúan registrando un **100% de nulos**. Las estructuras de almacenamiento siguen preparadas en el archivo físico Parquet, pero el cobro e ingesta de estas dimensiones se mantiene inactivo a nivel gubernamental.