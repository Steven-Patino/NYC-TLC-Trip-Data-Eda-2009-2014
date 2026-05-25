# Reporte de Consistencia Lógica, Temporal y Financiera (2009 - 2014)

Este documento detalla los resultados de la auditoría de estrés lógico realizada sobre los universos de datos consolidados de **Yellow Taxi** y **Green Taxi** dentro de la ventana temporal acotada del proyecto (**1 de enero de 2009 al 31 de diciembre de 2014**). El análisis evaluó la coherencia cronológica y las reglas de negocio financieras sobre un total acumulado de **1,041,244,411 registros**.

---

## 📊 1. Cuadro de Mando de Inconsistencias Lógicas

| Métrica de Calidad Evaluada | Yellow Taxi (2009-2014) | Green Taxi (2014) | Diagnóstico Técnico Inicial |
| :--- | :---: | :---: | :--- |
| **Universo Total Analizado** | **1,025,407,402** | **15,837,009** | Base Transaccional Escaneada |
| 📅 Viajes Pickup Fuera de Rango | 0 | 0 | 🟢 Perfecta delimitación de ingesta. |
| 📅 Viajes Dropoff Fuera de Rango | 2,292 | 661 | 🟡 Desborde de fin de año / Bugs de hardware. |
| ⏳ Paradojas Temporales ($Dropoff < Pickup$) | **284,917** | **202** | 🔴 Desconfiguración de Relojes / Corrupción. |
| 👥 Pasajeros Negativos | 0 | 0 | 🟢 Consistencia lógica impecable. |
| 🛣️ Distancia de Viaje Negativa | 3 | 0 | 🟢 Despreciable (Ruido aislado). |
| 💵 Tarifa Base Negativa (`fare_amount`) | **39,666** | **11,626** | 🔵 Contabilidad Espejo (Disputas / Cancelaciones) |
| 🪙 Propina Negativa (`tip_amount`) | 628 | 169 | 🟡 Ajustes marginales de pasarela de pago. |
| 💰 Monto Total Negativo (`total_amount`) | **39,661** | **11,626** | 🔵 Correlación directa con Tarifa Base Negativa. |

---

## 🔍 2. Desglose de Hallazgos y Patrones de Negocio

### ⏳ 2.1. El Fenómeno de las Paradojas Temporales
* **Yellow Taxi:** Registra **284,917 viajes** donde el pasajero llegó a su destino *antes* de haberse subido al taxi. Aunque representa apenas el **0.027%** del universo masivo, numéricamente destruye cualquier modelo de optimización de tiempos de viaje o cálculo de velocidades promedio. 
* **Origen:** Corrupción de datos por pérdida de sincronización de los satélites GPS en los "cañones urbanos" de Manhattan, o reinicios de software del taxímetro en plena marcha.

### 💵 2.2. Simetría Contable en Valores Negativos (Contracargos)
* Notemos la perfecta correlación matemática en la flota **Green Taxi**: **11,626** tarifas negativas se corresponden exactamente con **11,626** totales negativos. En la flota **Yellow**, la relación es casi idéntica (39,666 vs 39,661).
* **Significado de Negocio:** No se trata de un error del sistema. Esta es la forma en que la pasarela de pagos de la TLC registra las **disputas de tarjetas de crédito (chargebacks)**, cancelaciones de viajes en curso o devoluciones manuales del conductor. Para no borrar el registro histórico original, el sistema introduce una fila "espejo" con signo negativo para saldar la contabilidad del día.

---

## 🛠️ 3. Directivas de Limpieza para la Capa de Confianza (`Trusted`)

Para blindar la fase de analítica avanzada y el entrenamiento de modelos de Machine Learning, el pipeline de DuckDB aplicará los siguientes filtros duros de higiene:

1. **Filtro de Ventana Temporal Estricto:** Eliminar los registros donde el `dropoff` supere el umbral del 31 de diciembre de 2014 (comportamiento residual de cambio de año).
2. **Erradicación de Paradojas:** Descartar de forma mandatoria las filas donde `dropoff_clean < pickup_clean`.
3. **Aislamiento de Registros Contables Negativos:** Las tarifas y montos totales menores o iguales a cero ($\le 0$) deben ser excluidos del set analítico principal, ya que representan viajes no realizados o transacciones revertidas que sesgan el cálculo del ticket promedio real de la ciudad.