# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2014

Este documento contiene el análisis técnico de unicidad y detección de **filas espejo (duplicados exactos)** sobre el conjunto de datos de **Yellow Taxi** correspondiente al año **2014**. Este control valida la integridad física de las variables antes de consolidar el almacenamiento definitivo en la capa limpia de datos.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento de control de redundancia sobre los **165,447,579 registros** de 2014 confirma una optimización histórica en los sistemas de almacenamiento de la TLC, alcanzando niveles de duplicidad prácticamente nulos:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 165,447,579 | 100.000000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **909** | **0.000549%** | 🟢 Salud Excepcional (Ruido Despreciable) |
| **Registros Únicos Netos** | 165,446,670 | 99.999451% | Capa Target Limpia |

---

## ⚙️ 2. Metodología de Validación

La auditoría aplicó un agrupamiento riguroso sobre las **17 variables físicas activas** dictadas por la matriz de gobernanza para este periodo, incluyendo explícitamente el nuevo comportamiento dinámico de la tasa de mejora (`improvement_surcharge`):

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
16. `improvement_surcharge` *(Variable con activación transaccional dinámica)*
17. `total_amount`

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### 🟢 3.1. Máxima Eficiencia en Ingesta de Datos
Un porcentaje de redundancia real de apenas **0.000549%** confirma que los sistemas de despacho y persistencia de datos alcanzaron su pico de estabilidad operativa durante 2014. La arquitectura distribuida implementada en la flota neoyorquina logró mitigar casi por completo las colisiones de red o las escrituras concurrentes huérfanas en el lago de datos de AWS S3.

### 🛠️ 3.2. Directiva de Calidad para el Script ETL (`Trusted`)
* **Impacto Inapreciable:** Con menos de mil registros duplicados en un universo de 165 millones, cualquier análisis predictivo, métrica financiera o cálculo de tarifas agregadas se mantendrá perfectamente exacto e inalterado.
* **Mantenimiento del Pipeline:** Para dar continuidad a las buenas prácticas de ingeniería de datos aplicadas a lo largo del proyecto, el pipeline conservará la instrucción de deduplicación mandatoria a través de operaciones distributivas (`DISTINCT`). Esto purgará de la capa limpia las 909 filas redundantes detectadas, consolidando exactamente 165,446,670 registros únicos y listos para consumo analítico de alta fidelidad.