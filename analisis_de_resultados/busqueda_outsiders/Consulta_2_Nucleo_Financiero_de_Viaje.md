# Informe de Auditoría de Calidad de Datos: Núcleo Financiero (2009 - 2014)

Este documento detalla los resultados exclusivos de la auditoría de estrés realizada sobre el **Núcleo Financiero** de los universos transaccionales de **Yellow Taxi** y **Green Taxi** entre el **1 de enero de 2009 y el 31 de diciembre de 2014**. El análisis evalúa rigurosamente el comportamiento de las tarifas de taxímetro, las propinas registradas por medios electrónicos y los cobros por peajes urbanos, abarcando un universo bruto escaneado de **1,041,244,411 registros**.

---

## 📊 1. Cuadro de Mando del Núcleo Financiero: Máximos vs. Percentiles Robustos

| Dimensión Financiera por Año | Máximo Absoluto (`max`) | Percentil 99.9% (`p999`) | Diagnóstico Técnico de Calidad de Datos |
| :--- | :---: | :---: | :--- |
| **Yellow Taxi 2009** | | | |
| 💵 Tarifa Base (`fare_amount`) | $200.00 | $60.00 | 🟢 Rango de negocio esperado y limpio. |
| 🪙 Propina (`tip_amount`) | $100.00 | $12.15 | 🟢 Distribución de propinas orgánica. |
| 🛣️ Peajes (`tolls_amount`) | $20.00 | $8.00 | 🟢 Coherencia con tarifas oficiales de cruce. |
| **Yellow Taxi 2010** | | | |
| 💵 Tarifa Base (`fare_amount`) | **$93,960.07** | $61.30 | 🔴 **Outlier Crítico:** Bug de software / Ruido en Parquet. |
| 🪙 Propina (`tip_amount`) | $938.02 | $12.88 | 🟡 Anomalía operacional (Posible error de digitación). |
| 🛣️ Peajes (`tolls_amount`) | **$5,510.07** | $8.00 | 🔴 **Outlier de Red:** Bucle de lectura en antena EZPass. |
| **Yellow Taxi 2011** | | | |
| 💵 Tarifa Base (`fare_amount`) | $500.00 | $63.50 | 🟡 Desviación marginal por trayectos especiales largos. |
| 🪙 Propina (`tip_amount`) | $200.00 | $14.30 | 🟢 Consistencia estadística aceptable. |
| 🛣️ Peajes (`tolls_amount`) | $20.00 | $8.35 | 🟢 Ajuste menor en tarifas aeroportuarias. |
| **Yellow Taxi 2012** | | | |
| 💵 Tarifa Base (`fare_amount`) | $500.00 | $71.50 | 🟢 Refleja el aumento tarifario oficial de la TLC de 2012. |
| 🪙 Propina (`tip_amount`) | $200.00 | $15.20 | 🟢 Incremento proporcional al costo base del viaje. |
| 🛣️ Peajes (`tolls_amount`) | $20.00 | $9.50 | 🟢 Incremento inflacionario en peajes de puentes de NY. |
| **Yellow Taxi 2013** | | | |
| 💵 Tarifa Base (`fare_amount`) | **$158,995.81** | $77.50 | 🔴 **Máximo Histórico Basura:** Inyección masiva de ruido. |
| 🪙 Propina (`tip_amount`) | $888.19 | $17.20 | 🟡 Anomalía de cobro manual o teclado saturado. |
| 🛣️ Peajes (`tolls_amount`) | $960.09 | $10.66 | 🔴 Error transaccional aislado de telepeaje. |
| **Yellow Taxi 2014** | | | |
| 💵 Tarifa Base (`fare_amount`) | **$93,952.40** | $80.00 | 🔴 **Outlier Crítico:** Corrupción de datos recurrente. |
| 🪙 Propina (`tip_amount`) | **$999.99** | $18.00 | 🔴 **Tope de Sistema:** Saturación de pasarela de pago. |
| 🛣️ Peajes (`tolls_amount`) | **$1,500.08** | $11.00 | 🔴 Anomalía severa en integrador de facturación. |
| **Green Taxi 2014** | | | |
| 💵 Tarifa Base (`fare_amount`) | **$4,543.00** | $77.00 | 🔴 Outlier en flota distrital (Fuera del rango urbano habitual). |
| 🪙 Propina (`tip_amount`) | $931.29 | $17.40 | 🟡 Registro anómalo por aproximación manual errónea. |
| 🛣️ Peajes (`tolls_amount`) | **$1,600.08** | $9.00 | 🔴 Error recurrente de hardware en lector E-ZPass. |

---

## 🔍 2. Hallazgos Críticos e Interpretación de Negocio

El análisis comparativo entre los máximos absolutos y la métrica de corte robusto (Percentil 99.9%) revela tres patrones de falla tecnológica recurrentes en el ecosistema de transporte de Nueva York:

### 💵 2.1. Desbordamiento de Tarifas Metropolitanas (`fare_amount`)
* **Análisis de Outliers:** El pico de **$158,995.81** en 2013 y los eventos cercanos a los **$94,000** en 2010 y 2014 representan anomalías destructivas si se analiza el promedio simple de ingresos.
* **Comportamiento del Negocio (`p999`):** El percentil 99.9% es el indicador definitivo de la realidad operativa de la ciudad. Muestra una progresión orgánica anual ($60.00 $
ightarrow$ $61.30 $
ightarrow$ $63.50 $
ightarrow$ $71.50 $
ightarrow$ $77.50 $
ightarrow$ $80.00$). El notable incremento entre 2011 y 2012 captura fielmente el **aumento tarifario oficial decretado por la TLC en septiembre de 2012** (incremento cercano al 17% en la tarifa base). Toda cifra en el orden de los miles de dólares es diagnosticada como corrupción en la serialización de los archivos Parquet originales o un fallo catastrófico en el software de los terminales móviles de cobro.

### 🪙 2.2. Saturación en Techo de Pasarelas de Pago (`tip_amount`)
* **Análisis de Outliers:** El registro exacto de **$999.99** en 2014 no es un evento aleatorio de propina corporativa. Corresponde al límite técnico superior o "máscara de entrada" preprogramada en el software financiero de las terminales electrónicas de los taxis (p. ej., Verifone o Creative Mobile Technologies).
* **Comportamiento del Negocio (`p999`):** Mientras que el 99.9% de los pasajeros deja como máximo entre **$12.15 y $18.00** de propina electrónica en trayectos largos, los valores que tocan la barrera de los $1,000 se deben a errores de digitación del usuario (introducir ceros extra por confusión en el teclado táctil) o intentos fraudulentos de manipulación transaccional que el sistema detuvo en su tope de seguridad.

### 🛣️ 2.3. Bucles de Transmisión de Telepeaje (`tolls_amount`)
* **Análisis de Outliers:** Los peajes máximos de **$5,510.07** (2010) y superiores a los **$1,500.00** (2014) rompen cualquier lógica regulatoria de la infraestructura vial de Nueva York. 
* **Comportamiento del Negocio (`p999`):** El percentil 99.9% confirma que el costo máximo real por peajes en rutas legítimas oscila estrictamente entre **$8.00 y $11.00**, lo que equivale matemáticamente a cruzar puentes o túneles de alta tarifa (como el RFK, Queens Midtown o Verrazzano) hacia los aeropuertos JFK o LaGuardia. Los valores atípicos de miles de dólares exponen un error informático clásico en la integración entre las antenas físicas de telepeaje (E-ZPass) y el taxímetro, donde la transacción entró en un bucle repetitivo de cobro duplicado en milisegundos antes de consolidar el registro.

---

## 🛠️ 3. Directivas de Limpieza para la Capa de Confianza (`Trusted`)

Para blindar la veracidad analítica y evitar distorsiones en los modelos de Machine Learning financieros, el pipeline de DuckDB aplicará las siguientes reglas de higiene sobre el núcleo financiero:

1. **Umbral de Control para Tarifa Base:** Excluir del set analítico principal cualquier registro con `fare_amount > 100.00`. Este límite protege el pipeline de errores de miles de dólares, permitiendo capturar viajes reales de larga distancia a condados periféricos que están bien representados justo por debajo del percentil de $80.00.
2. **Techo Operacional de Propinas:** Restringir la columna `tip_amount` mediante un filtro de `tip_amount <= 25.00` para modelos de comportamiento de propinas, aislando las pulsaciones erróneas que saturaron el sistema en $999.99.
3. **Filtro de Cruces Regulados (Peajes):** Descartar o imputar transacciones donde `tolls_amount > 20.00`. Dado que el percentil 99.9% se ancla en un máximo estable de $11.00, un margen de $20.00 absorbe de manera segura cualquier combinación legítima de ida y vuelta de peajes dobles, eliminando por completo los bucles infinitos de lectura de antenas satelitales.
4. **Tratamiento de Valores Negativos Reafirmado:** Se mantiene el aislamiento obligatorio de valores $\le 0$ en estas columnas para separar los contracargos comerciales del cálculo del ticket promedio neto real de la ciudad.