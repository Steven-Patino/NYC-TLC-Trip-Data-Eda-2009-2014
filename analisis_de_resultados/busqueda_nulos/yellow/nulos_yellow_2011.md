# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Yellow Taxi 2011

Este documento contiene el diagnóstico técnico de integridad y completitud estructural del conjunto de datos de **Yellow Taxi** correspondiente al año **2011**. El análisis abarca un volumen histórico total de **176,887,259 registros** procesados mediante DuckDB directamente desde el lago de datos en AWS S3. El periodo marca formalmente la adopción del esquema de zonas geoespaciales y la arquitectura de campos unificada TPEP.

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero detalla el comportamiento de las variables analíticas e impositivas del proyecto presentes en el almacenamiento físico para el año 2011:

| # | Nombre de la Columna | Registros Nulos | Porcentaje de Nulos | Estado Analítico / Diagnóstico |
| :-: | :--- | :-: | :-: | :--- |
| **1** | `VendorID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **2** | `tpep_pickup_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **3** | `tpep_dropoff_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **4** | `passenger_count` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **5** | `trip_distance` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **6** | `PULocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas Estables) |
| **7** | `DOLocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas Estables) |
| **8** | `RatecodeID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **9** | `payment_type` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **10** | `fare_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **11** | `extra` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **12** | `mta_tax` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **13** | `tip_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **14** | `tolls_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **15** | `total_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **16** | `improvement_surcharge`| 0 | 0.0% | 🟢 Completitud Perfecta |
| **17** | `store_and_fwd_flag` | 86,790,597 | **49.0655%** | 🟡 Intermitencia Técnica Esperada |
| **18** | `congestion_surcharge` | 176,887,257 | **100.0%** | 🔴 Estructura Vacía (Sin Recaudación) |
| **--** | `airport_fee` | 176,887,258 | **100.0%** | 🔴 Estructura Vacía (Sin Recaudación) |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico Temporal

### 🟢 2.1. Éxito en la Transición a Esquema de Zonas
Las variables `PULocationID` y `DOLocationID` debutan en este periodo con un **0% de registros nulos**, confirmando que el 100% de los viajes del año ya fueron procesados bajo la nueva arquitectura de IDs de zonas de la TLC, eliminando la necesidad de manejar coordenadas de latitud/longitud en este bloque.

### 🟡 2.2. Comportamiento de la Bandera de Conexión (`store_and_fwd_flag`)
La presencia de un **49.0655% de nulos** no representa una falla en el pipeline de datos, sino un reflejo operativo: cerca de la mitad de las transacciones ocurrieron con conexión directa a los servidores centrales de despacho, mientras que la otra mitad requirió almacenamiento local temporal en el vehículo.

### 🔴 2.3. Bloques Impositivos en Estado Pasivo
Las columnas `congestion_surcharge` y `airport_fee` ya existen en el esquema de los archivos Parquet (lo que evitó caídas en la consulta), pero sus registros se encuentran vacíos en un $100\%$. Esto ratifica que, aunque la infraestructura de base de datos estaba lista, la política de cobro para estos conceptos aún no había entrado en vigencia.