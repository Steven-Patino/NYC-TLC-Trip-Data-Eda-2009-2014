# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Yellow Taxi 2014

Este documento contiene el diagnóstico técnico de integridad y completitud estructural del conjunto de datos de **Yellow Taxi** correspondiente al año **2014**. El análisis consolida un volumen histórico de **165,447,579 registros** procesados mediante DuckDB desde el lago de datos en AWS S3. Este periodo es crítico porque marca el inicio de la recaudación activa de tasas impositivas modernas.

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero presenta el estado de penetración de nulos para las variables del ecosistema del proyecto durante el año 2014:

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
| **16** | `improvement_surcharge`| 53,750,500 | **32.4879%** | 🔵 Activación Fiscal a Mitad de Año |
| **17** | `store_and_fwd_flag` | 50,120,238 | **30.2937%** | 🟡 Reducción de Latencia de Red |
| **18** | `congestion_surcharge` | 165,447,577 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |
| **--** | `airport_fee` | 165,447,579 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico Temporal

### 🔵 2.1. Despertar de la Tasa de Mejora (`improvement_surcharge`)
El dato clave de este reporte es el **32.4879% de nulos** en la columna de recargo por mejora. Esto significa que aproximadamente el $67.51\%$ de las filas del año ya contienen transacciones monetarias reales. Estructuralmente, la TLC comenzó a aplicar y registrar este cobro extra a las tarifas de forma mandatoria en el transcurso de 2014, rompiendo la inactividad de los tres años anteriores.

### 🟡 2.2. Optimización de la Cobertura de Hardware (`store_and_fwd_flag`)
La tasa de nulos en la bandera de conexión bajó drásticamente del ~49% (visto en 2011-2013) a un **30.2937%**. Esta variación positiva indica una modernización en las antenas de transmisión celular de la flota transaccional, logrando que el 70% de los viajes se enviaran en tiempo real sin requerir almacenamiento local en caché.

### 🔴 2.3. Latencia Remanente de Recargos
Los campos `congestion_surcharge` y `airport_fee` persisten al **100% nulos**. Al igual que en los periodos previos, sus contenedores en el esquema Parquet están estables, pero la política impositiva para la congestión del distrito central y el recargo aeroportuario sigue suspendida.