# medallion-cold-chain-pipeline

SenseTemp is an end-to-end Azure Databricks data engineering project for biomedical cold chain monitoring. It applies a Medallion architecture to ingest telemetry, validate and enrich sensor readings, classify thermal alerts, and publish reporting outputs suitable for operational analysis.

This portfolio version was prepared for public publication. It preserves the project logic and technical structure while removing sensitive credentials and replacing environment-specific values with safe placeholders.

## Business problem

Cold chain monitoring is critical in biomedical environments because temperature deviations can compromise medicines, vaccines, laboratory samples, or other temperature-sensitive assets. The objective of this project is to transform raw telemetry into reliable operational information that helps detect out-of-range events quickly and supports traceability.

## Solution overview

The project follows the Medallion pattern:

* 🟤 Bronze: ingest raw telemetry JSON and sensor metadata CSV
* ⚪ Silver: apply data quality rules, reject invalid records, and enrich telemetry with metadata
* 🟡 Gold: classify alerts, calculate deviations, summarize sensor behavior, and prepare visualization-ready outputs

## Repository structure

```text
medallion-cold-chain-pipeline/
├── notebooks/
│   ├── 01_bronze_ingestion.ipynb
│   ├── 02_silver_quality_enrichment.ipynb
│   └── 03_gold_reporting_visualization.ipynb
├── data/
│   └── sample/
│       ├── telemetry_sample.json
│       └── sensores_limites_sample.csv
├── outputs/
│   └── sample/
│       ├── alert_summary_sample.csv
│       └── gold_report_sample.csv
├── images/
│   ├── Architecture_diagram_flow.png
│   ├── Azure_Storage_Account.drawio.png
│   ├── detailed_temperature_report_delta.png
│   ├── read_&_display_data.png
│   └── sensor_trend_sample.png
├── docs/
│   ├── architecture_diagram_guide.md
│   ├── functional_specifications.md
│   └── repository_descriptions.md
└── README.md
```

## Notebooks included

* `01_bronze_ingestion.ipynb`: raw ingestion and secure configuration pattern
* `02_silver_quality_enrichment.ipynb`: validation rules, rejected records, curated outputs, and metadata enrichment
* `03_gold_reporting_visualization.ipynb`: alert logic, summary reporting, and temperature trend visualization

## Main data quality rules

The Silver layer demonstrates practical controls that are common in real-world telemetry pipelines:

* invalid or null timestamps are rejected
* null device names are rejected
* placeholder sensor values such as `999` are flagged as invalid
* telemetry columns without matching metadata are rejected from curated processing
* only validated records are promoted to downstream layers

## Gold outputs

The Gold layer produces business-ready outputs such as:

* alert status by reading (`OK`, `BAJA`, `ALTA`)
* deviation from allowed thresholds
* summary metrics by sensor and location
* trend visualization to support quick interpretation

## Technologies used

* Databricks
* Apache Spark
* PySpark
* Delta Lake
* Azure Blob Storage paths with secure configuration pattern
* Medallion architecture

## Security and publication notes

This public version intentionally avoids exposing confidential information:

* real credentials were removed
* storage account details were replaced with placeholders
* only sanitized samples should be published
* full production Delta tables should not be uploaded

When adapting this repository to your own environment, use Databricks secrets or another approved secret-management mechanism.

## Suggested screenshots for the repository

Add images in the `images/` folder for:

* architecture overview
* Gold report sample
* sensor temperature trend chart

These visuals help recruiters and reviewers understand the project quickly.

## Portfolio value

This project demonstrates:

* end-to-end pipeline design
* practical data quality engineering
* metadata-driven enrichment
* business-oriented alerting logic
* publishable project documentation and presentation readiness

## Author note

This repository was prepared as a portfolio project to demonstrate data engineering skills in Databricks with a realistic cold chain monitoring use case.
