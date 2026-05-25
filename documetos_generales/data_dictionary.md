# DATA_DICTIONARY.md

Este documento contiene la especificación técnica y funcional unificada de los campos presentes en los conjuntos de datos de la Comisión de Taxis y Limusinas de Nueva York (NYC TLC) para el periodo histórico de **2009 a 2015**. Está diseñado para servir como la única fuente de verdad (*Single Source of Truth*) para los equipos de Analytics, Data Engineering y BI, documentando las mutaciones y tipos de datos nativos del formato Parquet detectados en la auditoría.

---

## 📑 1. Categorías Funcionales

Para simplificar el gobierno de datos y el modelado físico en la capa analítica, las columnas del ecosistema (Yellow, Green y FHV) se agrupan en 7 categorías conceptuales:

1. **Proveedor:** Identificación de las empresas o bases de despacho que reportan la operación.
2. **Tiempo:** Fechas y marcas temporales asociadas al ciclo de vida del viaje.
3. **Ubicación:** Coordenadas geoespaciales (2009-2010) o identificadores de zona TLC (2011-2015).
4. **Viaje:** Métricas dimensionales, de volumen y volumetría de ocupación del trayecto.
5. **Operación:** Atributos regulatorios, banderas de conectividad y tipos de servicio.
6. **Pago:** Desglose financiero detallado, tasas, recargos e impuestos.
7. **Auditoría:** Metadatos internos y flags residuales de control del sistema de almacenamiento.

---

## 📊 2. Diccionario de Datos Global por Categoría

### 2.1. Proveedor
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `VendorID` / `vendor_id` / `vendor_name` | INT64 / BYTE_ARRAY | Proveedor | Identificador del proveedor de tecnología que capturó los datos del viaje. | **Mutación:** En 2009 inició como texto (`vendor_name`), en 2010 mutó a `vendor_id` (`BYTE_ARRAY`) y de 2011 a 2015 se estandarizó en enteros (`INT64`). |
| `dispatching_base_num` | BYTE_ARRAY | Proveedor | Código de la base de radiotaxi que despachó el servicio. | **Exclusivo de FHV (2015).** Llave primaria de la base emisora (ej. 'B02512'). Campo mandatorio. |
| `Affiliated_base_number` | BYTE_ARRAY | Proveedor | Código de la base afiliada físicamente al vehículo que operó el viaje. | **Exclusivo de FHV (2015).** Presenta valores nulos cuando el viaje fue operado por la misma base despachadora. |

### 2.2. Tiempo
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `Trip_Pickup_DateTime` / `pickup_datetime` / `tpep_pickup_datetime` / `lpep_pickup_datetime` | BYTE_ARRAY / INT64 | Tiempo | Fecha y hora exacta en la que se activó el taxímetro (inicio del viaje). | **Colisión Crítica:** En 2009-2010 venía como texto o timestamp crudo (`BYTE_ARRAY`). En 2011-2015 mutó a Unix Epoch de microsegundos (`INT64`). Yellow usa prefijo `tpep_` y Green usa `lpep_`. |
| `Trip_Dropoff_DateTime` / `dropoff_datetime` / `tpep_dropoff_datetime` / `lpep_dropoff_datetime` | BYTE_ARRAY / INT64 | Tiempo | Fecha y hora exacta en la que se detuvo el taxímetro (fin del viaje). | Mismas variaciones nominales y de tipo de dato que el pickup temporal. Crítico para calcular la métrica derivada de duración del trayecto. |

### 2.3. Ubicación
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `Start_Lon` / `pickup_longitude` / `Start_Lat` / `pickup_latitude` | DOUBLE | Ubicación | Coordenadas GPS exactas de inicio del viaje (Longitud / Latitud). | **Quiebre Temporal:** Activas únicamente en la era **2009-2010** en Yellow Taxi. Desaparecen por completo del dataset en 2011. |
| `End_Lon` / `dropoff_longitude` / `End_Lat` / `dropoff_latitude` | DOUBLE | Ubicación | Coordenadas GPS exactas de destino del viaje (Longitud / Latitud). | Activas únicamente en la era **2009-2010** en Yellow Taxi. |
| `PULocationID` / `PUlocationID` | INT64 / DOUBLE | Ubicación | ID numérico de la zona geográfica TLC donde se inició el viaje. | **Quiebre Temporal:** Reemplaza a las coordenadas GPS a partir de **2011** en Yellow y **2014** en Green. **Trampa FHV:** En FHV (2015) se guardó como `DOUBLE`. Requiere `CAST` a `INT64`. |
| `DOLocationID` / `DOlocationID` | INT64 / DOUBLE | Ubicación | ID numérico de la zona geográfica TLC donde finalizó el viaje. | Mismo comportamiento de sustitución geoespacial y colisión de tipos en FHV que `PULocationID`. |

### 2.4. Viaje
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `Passenger_Count` / `passenger_count` | INT64 | Viaje | Número de pasajeros declarados por el conductor en el taxímetro. | Cambia de *CamelCase* a minúsculas en 2010. Puede contener valores atípicos analíticos (0 o valores > 6 pasajeros). Inexistente en FHV. |
| `Trip_Distance` / `trip_distance` | DOUBLE | Viaje | Distancia lineal total recorrida durante el trayecto, medida en millas por el odómetro. | Cambia de *CamelCase* a minúsculas en 2010. Registra valores en 0 en viajes cancelados o con fallas mecánicas. Inexistente en FHV. |

### 2.5. Operación
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `Rate_Code` / `rate_code` / `RatecodeID` | DOUBLE / BYTE_ARRAY / INT64 | Operación | Código regulatorio de la tarifa aplicada al trayecto (ej. JFK, Newark). | **Mutación Extrema:** En 2009 era `DOUBLE`, en 2010 pasó a `BYTE_ARRAY` y luego a `INT64`, estabilizándose a partir de 2011 como `RatecodeID` (`INT64`). |
| `trip_type` | DOUBLE / INT64 | Operación | Indicador del tipo de asignación del servicio (Calle vs Despacho por App/Teléfono). | **Exclusivo de Green Taxi.** Presenta una anomalía de tipo de dato: 23 meses registrado como `DOUBLE` y **1 mes maldito en 2015** guardado como `INT64`. |
| `SR_Flag` | INT32 | Operación | Indicador binario de viaje compartido (*Shared Ride*). | **Exclusivo de FHV (2015).** Métrica de negocio clave para evaluar consolidación de viajes en plataformas privadas. |

### 2.6. Pago
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `Payment_Type` / `payment_type` | BYTE_ARRAY / INT64 | Pago | Método de transacción utilizado para liquidar el viaje. | **Colisión de Tipos:** En 2009-2010 era string (`BYTE_ARRAY`, ej. `'CRD'`, `'CSH'`). Desde 2011 mutó de forma permanente a IDs numéricos (`INT64`, ej. `1`, `2`). Inexistente en FHV. |
| `Fare_Amt` / `fare_amount` | DOUBLE | Pago | Costo base del viaje calculado por el algoritmo de tiempo y distancia recorrida. | Excluye propinas, peajes e impuestos. Inexistente en FHV. |
| `surcharge` / `extra` | DOUBLE | Pago | Cargos adicionales fijos por horas pico (rush hour) y nocturnidad. | Mutó de `surcharge` (2009-2010) a `extra` (2011-2015). Inexistente en FHV. |
| `mta_tax` | DOUBLE | Pago | Impuesto gubernamental automático de $0.50 destinado a la Metropolitan Transportation Authority. | **Único Sobreviviente:** 100% estable en Yellow y Green. Inexistente en FHV. |
| `Tip_Amt` / `tip_amount` | DOUBLE | Pago | Monto de la propina otorgada de forma voluntaria por el pasajero. | **Sesgo Analítico:** Las propinas en efectivo no se registran; este campo refleja con fidelidad el comportamiento transaccional en tarjetas de crédito. Inexistente en FHV. |
| `Tolls_Amt` / `tolls_amount` | DOUBLE | Pago | Sumatoria total de peajes físicos y electrónicos liquidados en el viaje. | Valor acumulativo reportado por el dispositivo TAG del vehículo. Inexistente en FHV. |
| `improvement_surcharge` | DOUBLE / INT32 | Pago | Tasa de mejora de $0.30 introducida por la TLC para financiar accesibilidad. | **Colisión en Yellow y Green:** Registrado durante meses específicos como `INT32` (8 meses en Green 2014, 1 mes en Yellow 2014); el resto del tiempo se guardó como `DOUBLE`. |
| `airport_fee` | INT32 / DOUBLE | Pago | Cargo adicional por concepto de origen/destino aeroportuario. | **Anomalía en Yellow:** Se registró en 2011 durante 1 mes como `DOUBLE`; los 59 meses restantes figura como `INT32`. Inexistente en FHV. |
| `congestion_surcharge` | INT32 / DOUBLE | Pago | Cargo fiscal por circular en la zona congestionada del bajo Manhattan. | **Inconsistencia:** Registrado como `DOUBLE` durante 5 meses (2011-2014) y estabilizado como `INT32` en el resto del periodo. |
| `Total_Amt` / `total_amount` | DOUBLE | Pago | Monto total neto cobrado al pasajero en el vehículo. | Fórmula transaccional base para validaciones de auditoría financiera:<br>$$\text{total\_amount} = \text{fare\_amount} + \text{extra} + \text{mta\_tax} + \text{tip\_amount} + \text{tolls\_amount} + \text{improvement\_surcharge}$$ |

### 2.7. Auditoría
| Column Name (Crudo) | Data Type (Nativo) | Categoría | Description | Notas Técnicas e Inconsistencias |
| :--- | :--- | :--- | :--- | :--- |
| `store_and_forward` / `store_and_fwd_flag` | DOUBLE / BYTE_ARRAY | Operación | Bandera que indica si el registro se guardó localmente en la memoria del taxi por falta de señal (Y/N). | **Mutación:** En 2009 venía como numérico (`DOUBLE`), de 2010 a 2015 se consolidó como una bandera de texto de un solo carácter (`BYTE_ARRAY`). Inexistente en FHV. |
| `__index_level_0__` | INT64 | Auditoría | Metadato residual con el índice de la tabla origen. | **Basura de Datos:** Aparece de forma exclusiva durante **2 meses en el año 2010** en Yellow Taxi. Evidencia un error de exportación histórica. Debe eliminarse en el ETL. |

---

## 🔢 3. Tablas de Referencia y Homologación (Catalog Mapping)

### 3.1. Métodos de Pago (`payment_type`)
Para unificar la era antigua (strings) con la era moderna (enteros) se debe aplicar el siguiente mapa de transformación estructural:

| Equivalente Numérico (`INT64`) | Código de Texto Histórico | Significado Funcional | Observaciones de Ingeniería |
| :---: | :--- | :--- | :--- |
| **1** | `CRD` | Credit card | Pago digital. Registra datos de propinas con precisión. |
| **2** | `CSH` | Cash | Pago manual. Campo `tip_amount` subrepresentado (tiende a 0). |
| **3** | `NOC` | No charge | Viajes institucionales o de cortesía regulada. |
| **4** | `DIS` | Dispute | Transacciones congeladas por disputas del usuario. |
| **5** | `UNK` | Unknown | Error de red del proveedor; estado no determinado. |
| **6** | `VOI` | Voided trip | Registros anulados antes del procesamiento de la tarifa. |

### 3.2. Códigos de Tarifa (`rate_code`)
Aplicable a las dimensiones Yellow y Green Taxi para interpretar el comportamiento de la demanda:

| ID Homologado | Significado Regulatorio | Regla de Negocio / Aplicación Territorial |
| :---: | :--- | :--- |
| **1** | Standard rate | Tarifa urbana base por taxímetro dentro de los 5 distritos de NYC. |
| **2** | JFK | Tarifa plana regulada para traslados desde/hacia el Aeropuerto JFK. |
| **3** | Newark | Tarifa especial que incluye peajes de retorno a New Jersey. |
| **4** | Nassau / Westchester | Tarifa interurbana para condados periféricos del norte y este de la ciudad. |
| **5** | Negotiated fare | Tarifas especiales pactadas legalmente fuera del taxímetro. |
| **6** | Group ride | Esquema de tarifa plana unificada para pasajeros en viajes compartidos. |