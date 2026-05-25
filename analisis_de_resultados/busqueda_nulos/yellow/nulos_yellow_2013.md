# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Yellow Taxi 2013

Este documento presenta el informe técnico de integridad y completitud estructural para el universo de datos de **Yellow Taxi** perteneciente al periodo **2013**. El análisis consolidó un total de **171,816,340 registros** procesados en caliente mediante DuckDB desde el almacenamiento en AWS S3.

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero resume el estado de penetración de nulos para las 18 variables del ecosistema del proyecto durante este año fiscal:

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
| **17** | `store_and_fwd_flag` | 85,415,283 | **49.7131%** | 🟡 Intermitencia Técnica Operativa |
| **18** | `congestion_surcharge` | 171,816,337 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |
| **--** | `airport_fee` | 171,816,340 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico Temporal

### 🟢 2.1. Consolidación de la "Era de Zonas"
Las columnas `PULocationID` y `DOLocationID` registran **cero nulos**, validando la solidez de la TLC al trackear los orígenes y destinos mediante identificadores de zonas de tráfico y dejando obsoletas las coordenadas geográficas tradicionales para el modelado de este año.

### 🟡 2.2. Continuidad Operativa en Caché (`store_and_fwd_flag`)
La métrica arroja un **49.7131% de registros nulos**, una constante matemática que venimos arrastrando desde 2011. Esto ratifica que prácticamente la mitad de la flota despacha lotes en diferido debido a las sombras de conectividad inalámbrica de la ciudad de Nueva York.

### 🔴 2.3. Latencia Fiscal Sostenida
Las variables `congestion_surcharge` y `airport_fee` persisten al **100% nulas** (con variaciones despreciables de 3 registros de control interno). El diseño lógico sigue preparado para recibir información, pero la lógica de negocio estatal aún mantiene apagada la recolección activa.