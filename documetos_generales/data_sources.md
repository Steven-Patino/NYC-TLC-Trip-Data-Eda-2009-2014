# DATA_SOURCES.md

Este documento detalla los orígenes, el linaje, la arquitectura de almacenamiento local en el Data Lake y las transformaciones críticas necesarias para la ingesta y unificación del ecosistema de datos de la *NYC Taxi and Limousine Commission (TLC)* para el periodo analítico de **2009 a 2015**.

---

## 📌 1. Información de Origen y Linaje Corporativo

- **Portal Oficial del Proveedor:** [NYC TLC Trip Record Data Portal](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- **Entidad Gubernamental Emisora:** New York City Taxi and Limousine Commission (TLC).
- **Herramientas de Extracción y Auditoría:** DuckDB Enterprise Engine + AWS S3 Connectors.
- **Formato de los Datos en Repositorio de Auditoría:** Archivos optimizados con codificación Parquet mensual (almacenamiento columnar).
- **Volumen Total Analizado:** 84 meses cronológicos continuos evaluados para reconstruir el linaje histórico.

---

## 🚖 2. Ecosistema de Servicios e Impacto en la Cobertura Temporal

La recolección de datos refleja la evolución regulatoria de la ciudad de Nueva York, dividiendo la carga de trabajo del pipeline en tres flujos asimétricos:

### 2.1. Yellow Taxi (Medallion Taxis)
- **Definición Operativa:** Taxis tradicionales amarillos con derecho exclusivo a recoger pasajeros en la vía pública en los cinco condados de NYC.
- **Ventana Temporal del Proyecto:** Cobertura total de **84 meses** (Enero 2009 a Diciembre 2015).
- **Densidad de Datos:** Representa el núcleo transaccional y financiero más grande del modelo.

### 2.2. Green Taxi (Street Hail Livery - SHL)
- **Definición Operativa:** Vehículos comunitarios creados para proveer servicio regulado exclusivamente en el norte de Manhattan (arriba de la calle 96th) y en los distritos periféricos (*Outer Boroughs*: Bronx, Brooklyn, Queens y Staten Island). Tienen estrictamente prohibido tomar pasajeros en la calle en los aeropuertos y en el distrito financiero del sur de Manhattan.
- **Ventana Temporal del Proyecto:** Cobertura de **24 meses** (Enero 2014 a Diciembre 2015). No contiene datos históricos previos, reflejando el nacimiento operacional del servicio.

### 2.3. For-Hire Vehicle (FHV)
- **Definición Operativa:** Flotas operadas por bases de despacho centralizadas, que engloban tanto a compañías tradicionales de radiotaxis corporativos como a las primeras integraciones masivas de plataformas digitales (Uber, Lyft). 
- **Ventana Temporal del Proyecto:** Cobertura de **12 meses** restringida estrictamente al año **2015** para mantener la coherencia analítica y temporal. Sus esquemas están orientados a auditoría de despacho administrativo y carecen de métricas transaccionales de costos.

---

## 📂 3. Arquitectura del Data Lake y Estructura de Almacenamiento

Para garantizar el cumplimiento de los principios de reproducibilidad, inmutabilidad y particionamiento eficiente de consultas sobre el motor analítico DuckDB, la jerarquía de directorios en el almacenamiento corporativo se define bajo el siguiente diseño físico:

```text
/data_lake
 ├── raw/                                     [Datos crudos / Inmutables]
 │    ├── yellow/
 │    │    ├── year=2009/
 │    │    │    ├── yellow_tripdata_2009-01.parquet
 │    │    │    └── ...
 │    │    └── year=2015/
 │    │         └── yellow_tripdata_2015-12.parquet
 │    ├── green/
 │    │    ├── year=2014/
 │    │    │    ├── green_tripdata_2014-01.parquet
 │    │    │    └── ...
 │    │    └── year=2015/
 │    │         └── green_tripdata_2015-12.parquet
 │    └── fhv/
 │         └── year=2015/
 │              ├── fhv_tripdata_2015-01.parquet
 │              └── ...
 ├── trusted/                                 [Datos normalizados / Esquema Fijo]
 │    └── nyc_tlc_consolidated_trips/
 │         ├── service_type=yellow/           [Particionamiento por tipo de servicio]
 │         │    ├── year=2009/
 │         │    └── ...
 │         ├── service_type=green/
 │         └── service_type=fhv/
 └── refined/                                 [Vistas Agregadas / Capa de Consumo BI]
      └── aggregated_taxi_metrics/
           ├── monthly_borough_trends.parquet
           └── economic_efficiency_metrics.parquet