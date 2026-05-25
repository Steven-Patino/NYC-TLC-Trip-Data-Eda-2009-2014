# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2011

Este documento detalla el análisis de unicidad y detección de **filas espejo (duplicados exactos)** realizado sobre el conjunto de datos de **Yellow Taxi** para el año **2011**. El objetivo es evaluar el nivel de redundancia estructural tras la migración masiva al esquema unificado TPEP y la eliminación de variables geográficas de latitud/longitud.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento de control de redundancia sobre los **176,887,259 registros** de 2011 demuestra una estabilización casi perfecta en los mecanismos de ingesta de la TLC, erradicando los problemas de doble inserción del año anterior:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 176,887,259 | 100.000000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **1,483** | **0.000838%** | 🟢 Salud Óptima (Ruido Despreciable) |
| **Registros Únicos Netos** | 176,885,776 | 99.999162% | Capa Target Limpia |

---

## ⚙️ 2. Metodología de Validación

La auditoría de unicidad se ejecutó aplicando un agrupamiento (`GROUP BY`) explícito sobre el nuevo bloque de **17 variables físicas activas** identificadas en la matriz de gobernanza para la era de zonas:

1. `VendorID`
2. `tpep_pickup_datetime`
3. `tpep_dropoff_datetime`
4. `passenger_count`
5. `trip_distance`
6. `PULocationID` *(Zona de Origen)*
7. `DOLocationID` *(Zona de Destino)*
8. `RatecodeID`
9. `store_and_fwd_flag`
10. `payment_type`
11. `fare_amount`
12. `extra`
13. `mta_tax`
14. `tip_amount`
15. `tolls_amount`
16. `improvement_surcharge`
17. `total_amount`

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### 🟢 3.1. Estabilización de los Canales de Ingesta
Un índice de redundancia de apenas **0.000838%** confirma que el sistema de recolección de datos de la TLC recuperó su comportamiento óptimo. Las dobles inserciones sistemáticas que afectaron al periodo 2010 (1.15%) fueron corregidas en origen por los ingenieros de la comisión al consolidar el nuevo pipeline basado en IDs de zonas (`PULocationID` / `DOLocationID`).

### 🛠️ 3.2. Recomendación para el Pipeline ETL (`Trusted`)
* **Criticidad Baja:** A diferencia de los periodos anteriores, la presencia de estas 1,483 filas espejo no genera distorsiones materiales en las métricas agregadas de negocio ni altera los cálculos de recaudación financiera.
* **Estrategia Estándar:** Para mantener la higiene matemática rigurosa de la arquitectura de datos, se sugiere mantener la directiva de deduplicación mediante `DISTINCT` o ventanas de limpieza en la transformación del año 2011. Esto removerá el remanente marginal y asegurará que la capa limpia consolide exactamente 176,885,776 registros únicos.