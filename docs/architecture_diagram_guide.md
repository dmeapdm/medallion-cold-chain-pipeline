# Architecture diagram guide

## Diagram flow
'[Data Generation & Streaming]'
`Simulator temperature IoT (VS Code - Python) -> Azure IoT Hub -> Azure Stream Analytics -> Raw telemetry JSON'

'[Medallion Processing & Analytics]'
'Raw telemetry JSON + sensor metadata CSV -> Bronze ingestion -> Silver validation and enrichment -> Gold alert classification and reporting -> dashboard/report consumers`

## Components to show

* telemetry source files in JSON
* metadata source file in CSV
* Bronze layer for raw ingestion
* Silver layer for data quality and metadata join
* Gold layer for alert logic, reporting, and visualization
* final consumers such as operations or quality monitoring teams

## Important labels

* Medallion architecture
* PySpark
* Delta outputs
* alert states: `OK`, `BAJA`, `ALTA`
