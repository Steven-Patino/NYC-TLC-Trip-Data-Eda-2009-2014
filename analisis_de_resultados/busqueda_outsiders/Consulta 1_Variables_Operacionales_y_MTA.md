# Reporte de Consistencia Lógica, Temporal, Financiera y Análisis de Outliers (2009 - 2014)

Este documento detalla los resultados de la auditoría de estrés lógico y el análisis avanzado de valores atípicos extremos (Outliers) realizados sobre los universos de datos consolidados de **Yellow Taxi** y **Green Taxi** dentro de la ventana temporal acotada del proyecto (**1 de enero de 2009 al 31 de diciembre de 2014**). El análisis evaluó la coherencia cronológica, las reglas de negocio financieras y los límites de hardware sobre un total acumulado de **1,041,244,411 registros**.

---

## 📊 1. Cuadro de Mando Integrado: Inconsistencias y Valores Extremos

| Métrica de Calidad / Dimensión Evaluada | Yellow Taxi (2009-2014) | Green Taxi (2014) | Diagnóstico Técnico e Impacto en Negocio |
| :--- | :---: | :---: | :--- |
| **Universo Total Analizado** | **1,025,407,402** | **15,837,009** | Base Transaccional Escaneada (Registros brutos). |
| 📅 Viajes Pickup Fuera de Rango | 0 | 0 | 🟢 Perfecta delimitación de ingesta temporal. |
| 📅 Viajes Dropoff Fuera de Rango | 2,292 | 661 | 🟡 Desborde de fin de año / Bugs residuales de hardware. |
| ⏳ Paradojas Temporales ($Dropoff < Pickup$) | **284,917** | **202** | 🔴 Desconfiguración de Relojes / Corrupción transaccional. |
| 👥 Pasajeros Negativos | 0 | 0 | 🟢 Consistencia lógica impecable en origen. |
| 👥 Pasajeros Máximos Registrados | **255** pasajeros | **9** pasajeros | 🔴 **Outlier Crítico:** Desborde de bits en memoria (`TINYINT`). |
| 👥 Pasajeros en Percentil 99.9% | **6.0** pasajeros | **6.0** pasajeros | 🟢 Umbral físico real compatible con vehículos SUV. |
| 🛣️ Distancia de Viaje Negativa | 3 | 0 | 🟢 Despreciable (Ruido aislado de transmisión). |
| 🛣️ Distancia Máxima Registrada | **134,619,063.1** mi | **688.1** mi | 🔴 **Outlier Crítico:** Corrupción de GPS o pérdida de señal en túneles. |
| 🛣️ Distancia en Percentil 99.9% | **23.31** mi | **22.64** mi | 🟢 Umbral operacional real (Viajes interdistritales / Aeropuertos). |
| 💵 Tarifa Base Negativa (`fare_amount`) | **39,666** | **11,626** | 🔵 Contabilidad Espejo (Disputas de tarjetas / Cancelaciones). |
| 🪙 Propina Negativa (`tip_amount`) | 628 | 169 | 🟡 Ajustes marginales de pasarela de pago. |
| 💵 Impuesto MTA Máximo Registrado | **$1,311.22** | **$7.35** | 🔴 **Outlier Crítico:** Taxímetros desconfigurados / Errores de digitación. |
| 💵 Impuesto MTA en Percentil 99.9% | **$0.50** | **$0.50** | 🟢 Consistencia absoluta con la tarifa plana oficial de NY. |
| 💰 Monto Total Negativo (`total_amount`) | **39,661** | **11,626** | 🔵 Correlación contable directa con Tarifa Base Negativa. |

---

## 🔍 2. Desglose de Hallazgos, Patrones de Negocio y Outliers Técnicos

### 👥 2.1. El Misterio Metodológico de los 255 Pasajeros (`passenger_count`)
* **El Hallazgo:** En la flota Yellow Taxi se observan registros con un límite absoluto de hasta **255 pasajeros** en un solo viaje. Sin embargo, el análisis estadístico revela que el percentil 99.9% (`p999_pasajeros`) se sitúa firmemente en **6.0 pasajeros** de manera consistente a lo largo de los años.
* **Explicación Técnica (Desborde de Bits):** El número 255 es el valor máximo almacenable en un campo de tipo entero de 8 bits sin signo (`unsigned TINYINT` o `byte`). Cuando el hardware del taxímetro sufre un microcorte de energía, un error de red o una desconfiguración del firmware, el sistema rellena por defecto el registro con un byte saturado de unos (`11111111`), lo que equivale matemáticamente a 255. Esto representa una anomalía puramente informática, no un evento real de negocio.

### 🗺️ 2.2. Viajes Intergalácticos y Desconfiguración del GPS (`trip_distance`)
* **El Hallazgo:** Los máximos históricos muestran valores absurdos, destacando un registro en 2014 con **134,619,063.1 millas** (una distancia equivalente a viajar más de 500 veces a la Luna). De manera opuesta, el percentil 99.9% se mantiene increíblemente estable entre **22.25 y 23.31 millas** en ambas flotas.
* **Explicación Técnica:** Estos valores extremos ocurren por pérdidas severas de telemetría GPS o fallos de inicialización del dispositivo transaccional (Mobile Data Terminal) al ingresar a los "cañones urbanos" formados por los rascacielos de Manhattan o túneles de conectividad (ej. Lincoln o Holland Tunnel). Al perder los puntos de referencia, el algoritmo calcula distancias basadas en coordenadas de error o ruido de red. El percentil 99.9% demuestra ser la métrica de corte perfecta para aislar el ruido sin sesgar los viajes legítimos a aeropuertos o zonas periféricas de Long Island.

### 💸 2.3. Simetría Contable y Anomalías de Impuestos (`mta_tax`)
* **Simetría de Negocio:** Se reconfirma una correlación matemática casi perfecta en los registros negativos (ej. Green Taxi con 11,626 tarifas negativas frente a 11,626 totales negativos). Esto convalida el proceso de **Contracargos (Chargebacks) y Disputas de Tarjetas de Crédito**, donde el sistema inserta filas inversas idénticas para balancear los libros contables diarios.
* **Anomalía del Impuesto MTA:** El impuesto fijado por la Autoridad Metropolitana del Transporte es plano y obligatorio de **$0.50**. Esto queda validado por el percentil 99.9% que da exactamente **0.50** en todas las mediciones. No obstante, el análisis de máximos detectó cobros atípicos de hasta **$1,311.22** en un solo viaje de Yellow Taxi en 2010. Esto evidencia fallos graves de digitación manual por parte del conductor en sistemas analógicos antiguos o bugs de redondeo en pasarelas electrónicas primitivas.

### ⏳ 2.4. Paradojas Temporales
* **El Hallazgo:** Se mantienen **284,917 viajes** en Yellow Taxi donde el tiempo de destino es menor al de origen ($Dropoff < Pickup$). Afecta al **0.027%** de los datos, lo cual destruye la velocidad promedio si no es controlado en el proceso de extracción, transformación y carga (ETL).

---

## 🛠️ 3. Directivas de Limpieza para la Capa de Confianza (`Trusted`)

Para blindar la fase de analítica avanzada, inteligencia de negocios y garantizar un entrenamiento libre de sesgos para modelos de Machine Learning, el pipeline de procesamiento en DuckDB aplicará las siguientes reglas duras de higiene de datos:

1. **Filtro de Ventana Temporal Estricto:** Eliminar los registros residuales donde el `dropoff` supere el límite del 31 de diciembre de 2014.
2. **Erradicación de Paradojas Temporales:** Descartar de forma mandatoria todas las filas donde la estampa de tiempo `dropoff_clean < pickup_clean`.
3. **Aislamiento de Registros Contables Negativos:** Excluir del set analítico principal todas las tarifas y montos totales menores o iguales a cero ($\le 0$), ya que representan transacciones revertidas que deforman el cálculo del ticket promedio real del sistema de transporte.
4. **Corte Técnico de Pasajeros (Imputación/Filtro):** Eliminar o marcar como registros corruptos aquellos viajes donde la cantidad de pasajeros sea estrictamente mayor a **6** (`passenger_count > 6`), limpiando el ruido provocado por el desborde de bits (255).
5. **Truncamiento por Percentil Avanzado para Distancias:** Aplicar un filtro de exclusión dinámico basado en el percentil 99.9%. Cualquier viaje cuya distancia supere las **24 millas** (`trip_distance > 24.0`) deberá ser removido del pipeline principal de entrenamiento de rutas, asegurando la eliminación total de los viajes erróneos de millones de millas detectados por fallas de GPS.
6. **Validación de Impuesto MTA:** Forzar que la columna `mta_tax` contenga únicamente el valor regulado de **$0.50** o valores financieros válidos de exención si aplicara ($0.00$), descartando cualquier cobro anómalo superior detectado en el análisis de máximos.
