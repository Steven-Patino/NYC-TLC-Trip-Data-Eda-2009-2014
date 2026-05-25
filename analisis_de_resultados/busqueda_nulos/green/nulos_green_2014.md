# Reporte de Calidad de Datos (EDA) - Matriz de Nulos Green Taxi 2014

Este documento presenta el diagnóstico técnico de integridad y completitud estructural del conjunto de datos de **Green Taxi (Boro Taxi)** para el año **2014**. El análisis procesó un volumen base de **15,837,009 registros** mediante DuckDB desde el lago de datos en AWS S3.

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero resume el estado de penetración de nulos para las variables del ecosistema Green Taxi durante su primer año consolidado:

| # | Nombre de la Columna | Registros Nulos | Porcentaje de Nulos | Estado Analítico / Diagnóstico |
| :-: | :--- | :-: | :-: | :--- |
| **1** | `VendorID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **2** | `lpep_pickup_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **3** | `lpep_dropoff_datetime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **4** | `passenger_count` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **5** | `trip_distance` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **6** | `PULocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas de Origen) |
| **7** | `DOLocationID` | 0 | 0.0% | 🟢 Completitud Perfecta (Zonas de Destino) |
| **8** | `RatecodeID` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **9** | `store_and_fwd_flag` | 0 | **0.0%** | 🟢 Control Total de Hardware |
| **10** | `payment_type` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **11** | `fare_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **12** | `extra` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **13** | `mta_tax` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **14** | `tip_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **15** | `tolls_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **16** | `total_amount` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **17** | `trip_type` | 1,778,917 | **11.2327%** | 🟡 Despacho Híbrido Requerido |
| **18** | `improvement_surcharge`| 14,191,086 | **89.6071%** | 🔴 Despliegue Fiscal Tardío |
| **19** | `congestion_surcharge` | 15,837,009 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |
| **--** | `ehail_fee` | 15,837,009 | **100.0%** | 🔴 Infraestructura Pasiva (Sin datos) |

---

## 🔍 2. Hallazgos Críticos del Diagnóstico de la Flota Verde

### 🟢 2.1. Robustez en Flag de Conexión (`store_and_fwd_flag`)
A diferencia de la flota Yellow (que históricamente arrastraba entre un 30% y 49% de nulos en esta bandera), la flota Green debuta con un **0.0% de nulos**. Esto demuestra que la homologación tecnológica de los Boro Taxis en 2014 obligó a los proveedores de software a inicializar el campo con valores booleanos de texto válidos (`Y` / `N`) en cada transacción.

### 🟡 2.2. Penalización Métrica en Tipo de Viaje (`trip_type`)
La variable registra un **11.2327% de nulos**. Al ser los Green Taxis vehículos habilitados tanto para responder a llamadas de radio/aplicación (*Dispatch*) como para levantar pasajeros en la calle (*Street-hail*), este porcentaje representa viajes donde la central de despacho o el taxímetro no logró indexar el origen del servicio. Deberá ser tratada en la capa de transformación.

### 🔴 2.3. Retraso en Impuestos y Tasas Digitales
* **`improvement_surcharge` (89.6071% de nulos):** Confirma que la tasa de mejora de 0.30 USD no se recolectó de forma masiva en los Boro Taxis durante la mayor parte de 2014, activándose en menos del 11% de los registros hacia el cierre del año.
* **`congestion_surcharge` y `ehail_fee` (100% de nulos):** Almacenamiento puramente pasivo. Los campos están listos en los archivos Parquet, pero sin capturar transacciones económicas reales.