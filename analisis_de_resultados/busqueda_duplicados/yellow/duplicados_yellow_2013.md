# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2013

Este documento detalla el análisis de unicidad y la identificación de **filas espejo (duplicados exactos)** sobre el conjunto de datos de **Yellow Taxi** para el periodo fiscal **2013**. El análisis tiene como fin asegurar la transparencia de los agregados económicos y mitigar riesgos de dobles contabilidades en los tableros analíticos.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento de control de duplicidad exacta sobre los **171,816,340 registros** de 2013 ratifica la estabilidad estructural de la ingesta de datos, manteniendo un nivel de redundancia insignificante:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 171,816,340 | 100.000000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **2,164** | **0.001259%** | 🟢 Salud Óptima (Ruido Operacional) |
| **Registros Únicos Netos** | 171,814,176 | 99.998741% | Capa Target Limpia |

---

## ⚙️ 2. Metodología de Validación

La auditoría de redundancia ejecutó una verificación cruzada a nivel de bloque binario mediante un agrupamiento estricto sobre las **17 variables físicas activas** dictadas por la matriz de gobernanza del proyecto para este ciclo:

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

### 🟢 3.1. Mitigación Exitosa de Errores en Origen
Un índice de duplicados exactos de apenas **0.001259%** ratifica que los canales de ingesta de la TLC operaron de forma óptima durante 2013. El comportamiento se alinea perfectamente con los estándares de calidad de 2011 y 2012, confirmando que las dobles inserciones masivas de la migración de 2010 están erradicadas del ecosistema de almacenamiento en AWS S3.

### 🛠️ 3.2. Reglas de Negocio para el Script ETL (`Trusted`)
* **Ausencia de Sesgo Métrico:** La persistencia de estas 2,164 filas espejo no tiene la capacidad de alterar los análisis de negocio, las proyecciones de demanda ni las auditorías contables de recaudación por propinas o tarifas base.
* **Higiene del Pipeline:** Siguiendo la disciplina técnica de las capas previas, se mantendrá la regla mandatoria de remoción de duplicados exactos empleando un paso de agregación distributiva (`DISTINCT`) en el pipeline de transformación. Esto purgará el remanente marginal y consolidará un universo neto y óptimo de 171,814,176 registros en la capa unificada.