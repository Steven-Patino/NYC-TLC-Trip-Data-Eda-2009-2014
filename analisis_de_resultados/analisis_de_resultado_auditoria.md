# Reporte Técnico: Auditoría Global de Esquemas e Inconsistencias (2009 - 2015)
**Datasets:** NYC Yellow Taxi, Green Taxi & For-Hire Vehicles (FHV)  
**Herramienta de Auditoría:** DuckDB + AWS S3 Engine  

---

## 📑 1. Introducción y Resumen Ejecutivo

Este reporte presenta los resultados consolidados de la auditoría de esquemas realizada sobre los archivos Parquet correspondientes al periodo histórico de 2009 a 2015 de la *Taxi and Limousine Commission (TLC)* de Nueva York. El universo bajo análisis abarca tres modalidades de transporte clave: **Yellow Taxi** (84 meses, 2009-2015), **Green Taxi** (24 meses, 2014-2015) y **For-Hire Vehicles** (12 meses, 2015).

Los datos analizados demuestran empíricamente que el ecosistema ha sufrido múltiples mutaciones estructurales, colisiones de tipos de datos y quiebres de nomenclatura debido a cambios de proveedores tecnológicos y actualizaciones de normativas a lo largo de los años. Este informe sirve como el **mapa genético unificado de los datasets**, un documento de gobernanza indispensable para guiar la fase de Limpieza, Transformación, Modelado y Análisis Exploratorio de Datos (EDA).

---

## 🚖 2. Módulo Yellow Taxi (2009 - 2015)

### 📊 2.1. Matriz de Consistencia de Esquemas
A continuación se listan todas las columnas que sufrieron algún tipo de variación en su nombre, tipo de dato nativo o periodo de actividad durante los 7 años analizados.

| Columna | Tipo de Dato Nativo | Total Meses Detectada | Periodo Activa | Estado Columna |
| :--- | :--- | :---: | :---: | :--- |
| **mta_tax** | DOUBLE | 84 | 2009 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **fare_amount** | DOUBLE | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **passenger_count** | INT64 | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **store_and_fwd_flag** | BYTE_ARRAY | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **tip_amount** | DOUBLE | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **tolls_amount** | DOUBLE | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **total_amount** | DOUBLE | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **trip_distance** | DOUBLE | 72 | 2010 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **DOLocationID** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **PULocationID** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **RatecodeID** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **VendorID** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **extra** | DOUBLE | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **payment_type** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **tpep_dropoff_datetime**| INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **tpep_pickup_datetime** | INT64 | 60 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **airport_fee** | INT32 | 59 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **improvement_surcharge**| DOUBLE | 59 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **congestion_surcharge** | INT32 | 55 | 2011 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **surcharge** | DOUBLE | 24 | 2009 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **End_Lat** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **End_Lon** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Fare_Amt** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Passenger_Count** | INT64 | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Payment_Type** | BYTE_ARRAY | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Rate_Code** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Start_Lat** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Start_Lon** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Tip_Amt** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Tolls_Amt** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Total_Amt** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Trip_Distance** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Trip_Dropoff_DateTime**| BYTE_ARRAY | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **Trip_Pickup_DateTime** | BYTE_ARRAY | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **dropoff_datetime** | BYTE_ARRAY | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **dropoff_latitude** | DOUBLE | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **dropoff_longitude** | DOUBLE | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **payment_type** | BYTE_ARRAY | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **pickup_datetime** | BYTE_ARRAY | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **pickup_latitude** | DOUBLE | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **pickup_longitude** | DOUBLE | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **store_and_forward** | DOUBLE | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **vendor_id** | BYTE_ARRAY | 12 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **vendor_name** | BYTE_ARRAY | 12 | 2009 a 2009 | ⚠️ MUTADA / INCONSISTENTE |
| **rate_code** | BYTE_ARRAY | 10 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **congestion_surcharge** | DOUBLE | 5 | 2011 a 2014 | ⚠️ MUTADA / INCONSISTENTE |
| **__index_level_0__** | INT64 | 2 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **rate_code** | INT64 | 2 | 2010 a 2010 | ⚠️ MUTADA / INCONSISTENTE |
| **airport_fee** | DOUBLE | 1 | 2011 a 2011 | ⚠️ MUTADA / INCONSISTENTE |
| **improvement_surcharge**| INT32 | 1 | 2014 a 2014 | ⚠️ MUTADA / INCONSISTENTE |

> 📌 **Nota sobre el único "Sobreviviente Indestructible":** La columna `mta_tax` fue la única que mantuvo una estabilidad absoluta de 84 meses (100% del tiempo) conservando tanto su nombre como su tipo de dato (`DOUBLE`).

### 📈 2.2. Análisis Técnico de los Hallazgos (Yellow Taxi)
A partir de la matriz de consistencia, los cambios estructurales se pueden agrupar en tres grandes quiebres temporales y metodológicos:

* **El Gran Quiebre de 2009 a 2010 (De CamelCase a Minúsculas):** En 2009 el set de datos utilizaba nomenclaturas basadas en mayúsculas iniciales y abreviaturas (*CamelCase*), pero a partir de 2010 se adoptó un estándar de minúsculas con guiones bajos (*snake_case*).
  * `Total_Amt` (12 meses en 2009) ➡️ Pasó a ser `total_amount` (72 meses de 2010 a 2015).
  * `Fare_Amt` ➡️ Pasó a ser `fare_amount`.
  * `Trip_Distance` ➡️ Pasó a ser `trip_distance`.
  * `Passenger_Count` ➡️ Pasó a ser `passenger_count`.
* **El Quiebre Radical de 2011 (Evolución de Geolocalización a IDs de Zona):** Este es el cambio con mayor impacto para el análisis geoespacial del EDA. En **2009 y 2010**, el dataset almacenaba coordenadas de geolocalización exactas a través de latitud y longitud (`Start_Lon`, `Start_Lat`, `End_Lon`, `End_Lat`). Del **2011 al 2015** (por 60 meses), las coordenadas GPS desaparecieron por completo. En su lugar, se implementó el sistema de regiones de la TLC mediante identificadores numéricos de zonas de taxi (`PULocationID` y `DOLocationID`). Asimismo, las columnas temporales adoptaron el prefijo del proveedor: `tpep_pickup_datetime` y `tpep_dropoff_datetime`.
* **Mutación y Colisión de Tipos de Datos:** Realizar un `UNION ALL` directo fallará debido a colisiones en los tipos de datos nativos de columnas homónimas:
  * **`payment_type`**: En 2009 y 2010 era de tipo texto (`BYTE_ARRAY` conteniendo strings como `'CRD'` o `'CSH'`). A partir de 2011 mutó a tipo numérico (`INT64` conteniendo IDs de catálogo como `1` o `2`).
  * **`airport_fee`**: Presenta una colisión de tipos; se registró en 2011 como decimal (`DOUBLE`) durante 1 mes, pero los 59 meses restantes figura como entero (`INT32`).
  * **`improvement_surcharge`**: Contiene 1 mes anómalo en 2014 registrado como entero (`INT32`), mientras que los otros 59 meses se almacenó como decimal (`DOUBLE`).
  * **Anomalía Interna (2010):** Se detectó la columna residual `__index_level_0__` durante únicamente 2 meses en 2010, lo que evidencia un error de exportación histórica (índices de Pandas adjuntos en la carga).

---

## 🟢 3. Módulo Green Taxi (2014 - 2015)

### 📊 3.1. Matriz de Consistencia de Esquemas
El servicio de Green Taxi (Boro Taxis) inició en el bucket a partir del año 2014.

| Columna | Tipo de Dato Nativo | Total Meses Detectada | Periodo Activa | Estado Columna |
| :--- | :--- | :---: | :---: | :--- |
| **DOLocationID** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **PULocationID** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **RatecodeID** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **VendorID** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **congestion_surcharge** | INT32 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **ehail_fee** | INT32 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **extra** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **fare_amount** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **lpep_dropoff_datetime**| INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **lpep_pickup_datetime** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **mta_tax** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **passenger_count** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **payment_type** | INT64 | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **store_and_fwd_flag** | BYTE_ARRAY | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **tip_amount** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **tolls_amount** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **total_amount** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **trip_distance** | DOUBLE | 24 | 2014 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **trip_type** | DOUBLE | 23 | 2014 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **improvement_surcharge**| DOUBLE | 16 | 2014 a 2015 | ⚠️ MUTADA / INCONSISTENTE |
| **improvement_surcharge**| INT32 | 8 | 2014 a 2014 | ⚠️ MUTADA / INCONSISTENTE |
| **trip_type** | INT64 | 1 | 2015 a 2015 | ⚠️ MUTADA / INCONSISTENTE |

### 📈 3.2. Análisis de Hallazgos y Particularidades (Green Taxi)
A diferencia del caos nominal inicial de los Taxis Amarillos, los Taxis Verdes nacen directamente bajo el estándar moderno de la TLC, reflejando una madurez tecnológica inmediata. Sin embargo, se identifican colisiones de tipos de datos:

* **Madurez Estructural:** Este dataset nunca registró latitudes ni longitudes exactas; adoptó desde el primer mes el uso de zonas geográficas (`PULocationID` y `DOLocationID`). Sus marcas temporales utilizan el prefijo operacional propio `lpep_`, y la columna `payment_type` es 100% estable como numérica (`INT64`).
* **Inconsistencias de Tipo Detectadas:**
  * **El Conflicto de `improvement_surcharge`:** Durante 8 meses del año 2014, esta tasa se calculó y guardó como un entero (`INT32`). Posteriormente, durante 16 meses entre 2014 y 2015, mutó a decimal (`DOUBLE`).
  * **La Anomalía de `trip_type`:** Esta columna (exclusiva de Green Taxi para diferenciar viajes por app/teléfono de los tomados en la calle) se registró durante 23 meses como decimal (`DOUBLE`). No obstante, existe **1 mes anómalo en 2015** donde el archivo se guardó como entero (`INT64`).

---

## 🚙 4. Módulo For-Hire Vehicles / FHV (Exclusivo 2015)

### 📊 4.1. Matriz de Consistencia de Esquemas
El servicio regulado de FHV (donde se encuentran Uber, Lyft y bases privadas) se analiza con foco estricto en sus 12 meses históricos de 2015 para mantener la simetría temporal del proyecto.

| Columna | Tipo de Dato Nativo | Total Meses Detectada | Periodo Activa | Estado Columna |
| :--- | :--- | :---: | :---: | :--- |
| **Affiliated_base_number**| BYTE_ARRAY | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **DOlocationID** | DOUBLE | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **PUlocationID** | DOUBLE | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **SR_Flag** | INT32 | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **dispatching_base_num**  | BYTE_ARRAY | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **dropOff_datetime** | INT64 | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |
| **pickup_datetime** | INT64 | 12 | 2015 a 2015 | 🟢 ESTABLE (Sin cambios) |

### 📈 4.2. Análisis de Hallazgos Críticos (El Choque de Modelos FHV)
Aunque internamente el esquema de FHV es 100% estable durante los 12 meses, su comparación con Yellow y Green Taxi revela un quiebre metodológico de gran relevancia:

* **El Peligro Oculto de las Zonas Geográficas (`DOUBLE` vs `INT64`):** En *Yellow* y *Green Taxi*, los IDs de zona son números enteros nativos (`INT64`). En contraste, en el dataset de FHV de 2015, la TLC almacenó `PUlocationID` y `DOlocationID` como decimales (`DOUBLE`), probablemente debido a la inferencia automática producida por la presencia de registros vacíos (`NaN`). Un `UNION` directo corromperá o truncará estas claves.
* **Anarquía en las Mayúsculas (CamelCase vs snake_case):** FHV rompe las convenciones previas al mezclar estilos nominales en un mismo archivo (ej. `PUlocationID` con la "l" minúscula, `dropOff_datetime` con la "O" mayúscula, o la coexistencia de `Affiliated_base_number` en mayúscula frente a `dispatching_base_num` en minúscula).
* **Ausencia Transaccional Financiera:** El dataset FHV no registra tarifas, propinas, impuestos ni recargos (`fare_amount`, `tip_amount`, etc. no existen). Las aplicaciones privadas no compartían su información de facturación con la TLC en 2015. Cuenta de manera exclusiva con la métrica de negocio `SR_Flag` (`INT32`) para identificar viajes compartidos (*Shared Rides*).

---

## 👑 5. Plan de Unificación Global (Yellow + Green + FHV)

Para estructurar una base de datos analítica homogénea y consolidada, se descarta cualquier estrategia de unificación directa. Se define el siguiente mapa de transformación modular y homologación estricta para el pipeline de datos (ETL):

### 📋 Mapeo y Directrices de Homologación Requeridos

| Dimensión Deseada | Origen Yellow (2009-2015) | Origen Green (2014-2015) | Origen FHV (2015) | CAST / Transformación Requerida en el Pipeline |
| :--- | :--- | :--- | :--- | :--- |
| **`id_proveedor`** | `VendorID` / `vendor_id` / `vendor_name` | `VendorID` | `dispatching_base_num` | Convertir a String (`VARCHAR`) para dar soporte a los códigos alfanuméricos de base FHV (ej. 'B02512') e IDs de taxi. |
| **`fecha_recogida`**| `tpep_pickup_datetime` / `Trip_Pickup_DateTime` | `lpep_pickup_datetime` | `pickup_datetime` | `CAST(columna AS TIMESTAMP)` |
| **`fecha_entrega`** | `tpep_dropoff_datetime` / `Trip_Dropoff_DateTime` | `lpep_dropoff_datetime` | `dropOff_datetime` | `CAST(columna AS TIMESTAMP)` |
| **`zona_origen_id`**| `PULocationID` / *Mapear desde Lat/Lon en 2009-2010* | `PULocationID` | `PUlocationID` | **CRÍTICO:** Forzar `CAST(columna AS INT64)` en FHV para homogeneizar con Taxis y remover el tipo `DOUBLE`. |
| **`zona_destino_id`**| `DOLocationID` / *Mapear desde Lat/Lon en 2009-2010* | `DOLocationID` | `DOlocationID` | **CRÍTICO:** Forzar `CAST(columna AS INT64)` en FHV para homogeneizar con Taxis y remover el tipo `DOUBLE`. |
| **`tipo_vehiculo`** | *Inyección estática:* `'YELLOW'` | *Inyección estática:* `'GREEN'` | *Inyección estática:* `'FHV'` | Agregar como columna literal/estática para segmentar la analítica global en el EDA. |
| **`monto_tarifa`**  | `fare_amount` / `Fare_Amt` | `fare_amount` | *No existe* | Asignar `NULL` o `0` para FHV. `CAST(columna AS DOUBLE)` para las demás modalidades. |
| **`monto_propina`** | `tip_amount` / `Tip_Amt` | `tip_amount` | *No existe* | Asignar `NULL` o `0` para FHV. `CAST(columna AS DOUBLE)` para las demás modalidades. |
| **`monto_total`**   | `total_amount` / `Total_Amt` | `total_amount` | *No existe* | Asignar `NULL` o `0` para FHV. `CAST(columna AS DOUBLE)` para las demás modalidades. |

### 🛠️ Estrategia de Ejecución por Eras Lógicas
1. **Pipeline Era 2009-2010 (Yellow):** Renombrar nomenclaturas *CamelCase*, procesar tipos de texto a fechas y aplicar la conversión o descarte de coordenadas GPS según reglas de negocio. Omitir la columna espuria `__index_level_0__` del año 2010.
2. **Pipeline Era 2011-2015 (Yellow & Green):** Homologar prefijos temporales (`tpep_` y `lpep_`) hacia los nombres estándar de negocio. Aplicar un `CAST` unificado a `DOUBLE` sobre las columnas financieras mutadas (`improvement_surcharge` y `airport_fee`).
3. **Pipeline Era 2015 (FHV):** Neutralizar la colisión geográfica convirtiendo los IDs flotantes a enteros, renombrar las variables respetando el estándar *snake_case* e inicializar los campos económicos ausentes en nulo o cero para garantizar la coherencia tabular en la tabla de hechos final.