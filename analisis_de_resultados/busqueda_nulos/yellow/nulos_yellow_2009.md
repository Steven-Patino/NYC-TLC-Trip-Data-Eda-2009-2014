# Reporte de Calidad de Datos (EDA) - Yellow Taxi 2009

Este documento contiene el diagnóstico técnico de integridad, completitud y redundancia estructural del conjunto de datos de **Yellow Taxi** correspondiente al año **2009**. El análisis abarca un volumen histórico total de **170,896,055 registros** procesados mediante DuckDB directamente desde el lago de datos en AWS S3.

---

## 📊 1. Matriz de Completitud (Análisis de Nulos)

El siguiente tablero detalla el comportamiento de las 18 columnas analíticas del proyecto. Se dividen según su estado de presencia física en los archivos del periodo evaluado:

| # | Nombre de la Columna | Registros Nulos | Porcentaje de Nulos | Estado Analítico |
| :-: | :--- | :-: | :-: | :--- |
| **1** | `vendor_name` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **2** | `Trip_Pickup_DateTime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **3** | `Trip_Dropoff_DateTime` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **4** | `Passenger_Count` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **5** | `Trip_Distance` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **6** | `Start_Lon` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **7** | `Start_Lat` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **8** | `End_Lon` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **9** | `End_Lat` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **10** | `Payment_Type` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **11** | `Fare_Amt` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **12** | `Tip_Amt` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **13** | `Tolls_Amt` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **14** | `Total_Amt` | 0 | 0.0% | 🟢 Completitud Perfecta |
| **15** | `Rate_Code` | 170,896,055 | **100.0%** | 🚨 Hallazgo Técnico (Vacío) |
| **16** | `congestion_surcharge` | 170,896,055 | **100.0%** | 🟡 Ausente por Era Temporal |
| **17** | `surcharge` | 170,896,055 | **100.0%** | 🟡 Ausente por Era Temporal |
| **18** | `mta_tax` | 170,896,055 | **100.0%** | 🟡 Ausente por Era Temporal |

---

## 🔍 2. Hallazgos y Conclusiones del Diagnóstico

### 🟢 2.1. Calidad Transaccional Óptima
Las dimensiones críticas de negocio (fechas de viaje, volumetría de pasajeros y distancias por odómetro) junto con el bloque financiero base (`Fare_Amt`, `Tip_Amt`, `Total_Amt`) registran un **0% de valores nulos**. Los datos crudos recolectados por los proveedores CMT y VeriFone para este año no presentan pérdidas de registros ni truncado de campos durante la transferencia original.

### 🚨 2.2. Anomalía Crítica en Códigos de Tarifa (`Rate_Code`)
La columna `Rate_Code` se encuentra **100% vacía** a lo largo de los 170.8 millones de filas. 
* **Impacto en Ingeniería/BI:** Durante el año 2009, no será posible clasificar los viajes bajo las reglas de negocio de la TLC (como identificar viajes planos al Aeropuerto JFK o tarifas especiales a Newark) utilizando esta variable nativa. Toda segmentación en este año deberá inferirse mediante reglas de cálculo geográfico o mantenerse como "No Clasificado".

### 🟡 2.3. Validación de Columnas Temporales Ausentes
Las variables `congestion_surcharge`, `surcharge` y `mta_tax` arrojan un 100% de nulos de forma esperada. Esto corrobora el linaje de datos: estas tasas impositivas y recargos regulatorios no formaban parte de la estructura física del esquema de la TLC en este periodo.

---

## 👥 3. Control de Redundancia (Filas Duplicadas Exactas)

> ⏳ **Estado:** Pendiente de ejecución.
>
> Para concluir el reporte de salud del año 2009, se requiere la ejecución del script de agrupación global (`GROUP BY ALL`) para determinar si existen registros transaccionales idénticos replicados (filas espejo) que requieran la implementación de un nodo de deduplicación en la capa `trusted`.