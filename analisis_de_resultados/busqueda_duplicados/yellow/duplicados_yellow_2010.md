# Reporte de Auditoría de Redundancia y Filas Espejo - Yellow Taxi 2010

Este documento detalla el análisis de unicidad y la detección de **filas espejo (duplicados exactos)** realizado sobre el conjunto de datos de **Yellow Taxi** para el periodo correspondiente al año **2010**. El objetivo es cuantificar y diagnosticar la redundancia estructural para asegurar que no existan desvíos en los agregados de BI ni dobles contabilidades financieras en el modelo final.

---

## 📈 1. Resumen Ejecutivo de Unicidad

El procesamiento de control de redundancia sobre los **169 millones de registros** de 2010 arrojó un incremento notable en las anomalías de replicación en comparación con los periodos históricos anteriores:

| Variable Analítica | Métrica Detallada | Impacto Porcentual | Estado de Salud |
| :--- | :---: | :---: | :--- |
| **Universo de Registros Evaluados** | 169,001,162 | 100.0000% | Volumen Base Crudo |
| **Filas Espejo Detectadas** | **1,959,393** | **1.1594%** | ⚠️ Alerta de Redundancia Frecuente |
| **Registros Únicos Netos** | 167,041,769 | 98.8406% | Capa Target Estimada |

---

## ⚙️ 2. Metodología de Detección

Debido a la inestabilidad de tipos de datos en la columna `rate_code` detectada en la fase de completitud, se forzó un aislamiento analítico explícito. La auditoría ejecutó una agrupación manual estricta sobre las **18 columnas físicas e implícitas** del ecosistema de archivos Parquet de 2010:

1. `vendor_id`
2. `pickup_datetime`
3. `dropoff_datetime`
4. `passenger_count`
5. `trip_distance`
6. `pickup_longitude`
7. `pickup_latitude`
8. `dropoff_longitude`
9. `dropoff_latitude`
10. `rate_code` *(Casteada a VARCHAR para unificar esquemas mutantes)*
11. `payment_type`
12. `fare_amount`
13. `surcharge`
14. `tip_amount`
15. `tolls_amount`
16. `total_amount`
17. `mta_tax`
18. `__index_level_0__` *(Incluida por fidelidad absoluta al bloque binario original)*

---

## 🔍 3. Conclusiones y Diagnóstico Técnico

### ⚠️ 3.1. Diagnóstico del Error de Inserción
Un índice de duplicados del **1.1594%** (casi 2 millones de registros espejo) denota un patrón sistemático. No se trata de fallas aisladas en los taxímetros. La causa raíz más probable es un fallo de orquestación histórica en la TLC, donde múltiples lotes de archivos Parquet mensuales sufrieron una doble ingesta o sobreescritura fallida durante el cambio estructural a `snake_case`.

### 🛠️ 3.2. Plan de Acción Crítico para el Pipeline ETL
* **Impacto Financiero Eliminado:** Permitir que estas 1.95 millones de filas avancen hacia los tableros analíticos distorsionaría gravemente la contabilidad de ingresos por propinas (`tip_amount`), tarifas base (`fare_amount`) y el volumen real de viajes de la ciudad de Nueva York.
* **Estrategia Mandatoria en Capa Trusted:** A diferencia de 2009, aquí el proceso de deduplicación es **crítico y obligatorio**. Se debe implementar un paso estricto de remoción de redundancias (usando `DISTINCT` o `ROW_NUMBER() OVER (...)`) en el script de transformación que procese el año 2010 para purgar estos 1.95 millones de registros excedentes y consolidar un universo netamente limpio de 167.04 millones de viajes.