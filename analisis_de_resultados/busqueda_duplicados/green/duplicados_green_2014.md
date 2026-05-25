# Reporte de Auditoría de Redundancia y Filas Espejo - Green Taxi 2014

Este documento contiene el análisis técnico de unicidad y la cuantificación de **filas espejo (duplicados exactos)** sobre el conjunto de datos de **Green Taxi** para el periodo fiscal **2014**. Este control de calidad valida que la introducción de la nueva flota de Boro Taxis mantenga la consistencia analítica antes de consolidar la capa unificada de datos del proyecto.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento de control de redundancia sobre los **15,837,009 registros** crudos de la flota verde en 2014 ratifica que el set de datos opera bajo condiciones de integridad física excepcionales:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 15,837,009 | 100.000000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **161** | **0.001017%** | 🟢 Salud Excepcional (Ruido Marginal) |
| **Registros Únicos Netos** | 15,836,848 | 99.998983% | Capa Target Limpia |

---

## ⚙️ 2. Metodología de Validación

La auditoría aplicó un agrupamiento estricto a nivel de bloque binario sobre las **18 variables físicas activas** del esquema Green Taxi para este ciclo, aislando el comportamiento dinámico y las mutaciones de las variables de negocio nativas:

1. `VendorID`
2. `lpep_pickup_datetime`
3. `lpep_dropoff_datetime`
4. `passenger_count`
5. `trip_distance`
6. `PULocationID`
7. `DOLocationID`
8. `RatecodeID`
9. `store_and_fwd_flag`
10. `payment_type`
11. `fare_amount`
12. `extra`
13. `mta_tax`
14. `tip_amount`
15. `tolls_amount`
16. `trip_type` *(Variable nativa de la flota verde)*
17. `improvement_surcharge` *(Variable con despliegue fiscal tardío)*
18. `total_amount`

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### 🟢 3.1. Heredabilidad Tecnológica Exitosa
Un índice de duplicación de solo **0.001017%** confirma que las especificaciones de hardware y los protocolos de transmisión exigidos por la TLC a los proveedores de la flota verde desde su lanzamiento fueron sumamente estrictos. Los Boro Taxis evitaron por completo las colisiones masivas de almacenamiento y los errores de orquestación concurrentes detectados en los inicios de la serie histórica de otras flotas.

### 🛠️ 3.2. Directiva de Calidad para el Pipeline ETL (`Trusted`)
* **Cero Sesgo Financiero:** Con apenas 161 filas redundantes en casi 16 millones de registros, el impacto en la agregación de KPIs de negocio, contabilidad de propinas o el cálculo de distancias promedio es nulo.
* **Higiene Estandarizada:** Manteniendo la homogeneidad en las reglas de calidad aplicadas a todo el lago de datos del proyecto (2009-2014), el script de transformación ejecutará la instrucción de deduplicación mandatoria mediante agregaciones distributivas (`DISTINCT`). Esto purgará el remanente marginal y asentará exactamente 15,836,848 registros únicos netos en la capa de consumo.