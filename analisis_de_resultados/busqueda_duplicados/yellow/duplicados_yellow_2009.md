# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2009

Este documento detalla el análisis de unicidad y la detección de **filas espejo (duplicados exactos)** realizado sobre el conjunto de datos de **Yellow Taxi** para el periodo correspondiente al año **2009**. El objetivo es garantizar que no existan registros transaccionales repetidos que distorsionen las métricas de BI o inflen los agregados financieros en el modelo analítico.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento transaccional de los **170.8 millones de registros** arrojó las siguientes métricas clave tras ejecutar un análisis de coincidencia absoluta en el bloque de variables físicas del set de datos:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 170,896,055 | 100.00% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **360** | **0.000211%** | 🟢 Unicidad Excelente |
| **Registros Únicos Netos** | 170,895,695 | 99.999789% | Capa Target Estimada |

---

## ⚙️ 2. Metodología de Detección

Debido a que el set de datos crudo en el año 2009 presenta columnas con valores ausentes programáticos (como `Rate_Code`), se anuló la inferencia automática del motor analítico para evitar falsos positivos masivos. 

La auditoría se realizó mediante una **estrategia de agrupación explícita**, forzando al motor a validar la igualdad absoluta en las **15 columnas físicas presentes** de la matriz original del proyecto:

1. `vendor_name`
2. `Trip_Pickup_DateTime`
3. `Trip_Dropoff_DateTime`
4. `Passenger_Count`
5. `Trip_Distance`
6. `Start_Lon`
7. `Start_Lat`
8. `End_Lon`
9. `End_Lat`
10. `Rate_Code`
11. `Payment_Type`
12. `Fare_Amt`
13. `Tip_Amt`
14. `Tolls_Amt`
15. `Total_Amt`

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### 3.1. Evaluación del Ruido Técnico
Un volumen de **360 filas duplicadas** sobre un universo que supera los 170 millones representa un ruido técnico **estadísticamente insignificante**. Esta anomalía suele originarse en el origen por reintentos de conexión en las antenas de transmisión de los vehículos (CMT/VeriFone) al enviar el fin del viaje al servidor central, o por fallos menores en la persistencia del almacenamiento inicial de la TLC.

### 🛠️ 3.2. Plan de Acción para Ingeniería de Datos (ETL)
* **Reconciliación Financiera:** Mantener estas 360 filas en el modelo final generaría un desvío imperceptible en los ingresos totales del año, pero rompería la consistencia matemática estricta del pipeline de datos.
* **Estrategia en Capa Trusted:** Se determina mandatorio aplicar una función de deduplicación selectiva utilizando una ventana analítica o un operador de agregación única (`DISTINCT`) sobre las columnas explícitas en el script de transformación, purgando estos registros de forma definitiva antes de estructurar la tabla de hechos unificada.