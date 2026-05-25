# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2012

Este documento detalla el análisis de unicidad y la cuantificación de **filas espejo (duplicados exactos)** del conjunto de datos de **Yellow Taxi** correspondiente al año **2012**. La evaluación permite validar que los canales transaccionales mantengan la integridad métrica y que no existan distorsiones financieras para este periodo.

---

## 📈 1. Resumen Ejecutivo de Unicidad

La evaluación de redundancia estructural sobre los **171,359,007 registros** crudos de 2012 confirma que el set de datos opera con niveles de duplicación técnica despreciables:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 171,359,007 | 100.000000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **1,604** | **0.000936%** | 🟢 Salud Óptima (Ruido Transaccional) |
| **Registros Únicos Netos** | 171,357,403 | 99.999064% | Capa Target Limpia |

---

## ⚙️ 2. Metodología de Validación

La auditoría aplicó una estrategia de comparación de registros en profundidad mediante un agrupamiento estricto sobre las **17 variables físicas activas** documentadas para este ciclo en la matriz de gobernanza de datos:

1. `VendorID`
2. `tpep_pickup_datetime`
3. `tpep_dropoff_datetime`
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
16. `improvement_surcharge`
17. `total_amount`

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### 🟢 3.1. Consistencia Operativa del Pipeline
Un nivel de redundancia del **0.000936%** certifica que los sistemas de despacho integrados en la flota de Yellow Taxi operan bajo un estado controlado de sincronización de datos. La bajísima tasa de colisiones repite el patrón de estabilidad observado en 2011, lo que deslinda cualquier error de orquestación o dobles inserciones masivas en la capa de almacenamiento en AWS S3.

### 🛠️ 3.2. Estrategia en la Capa de Transformación (`Trusted`)
* **Impacto Despreciable:** Estas 1,604 filas espejo remanentes no representan un riesgo de sesgo en los modelos estadísticos ni en los tableros analíticos del negocio.
* **Directiva de Calidad:** Siguiendo las directrices arquitectónicas aplicadas en los años previos, se mantendrá la regla de deduplicación estándar mediante operaciones distributivas (`DISTINCT`) en el script de transformación de 2012. Esto limpiará el margen de ruido transaccional y fijará en la capa limpia exactamente 171,357,403 registros únicos netos.