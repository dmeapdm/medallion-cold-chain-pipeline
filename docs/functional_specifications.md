## Case Description

The Dr. Horacio Heller Public Hospital, located in the province of Neuquén, provides essential services for the population of the western side of the capital city and the Confluencia Region, outstandingly in areas such as neonatology, immunization (vaccines), and the clinical analysis laboratory. These services rely on biomedical and refrigeration equipment that requires strict thermal control and traceability, including incubators, refrigerators, freezers, and ultra-low temperature freezers (ultra-freezers).

In this context, the temperature sensing and monitoring project arises from the need to oversee the thermal conditions of this equipment, guaranteeing the proper preservation of biological products, vaccines, and sensitive supplies. Furthermore, it aims to establish data traceability through continuous data logging, automated reporting, and the detection of out-of-range thermal alerts.

The project development was conceived in three distinct phases:
* **Phase 1:** Focused on local prototyping using IoT technology, utilizing an Arduino Nano board as the data acquisition system alongside digital temperature sensors. In this stage, data processing was executed locally using Python, enabling the visualization of temperature trends and the storage of readings in CSV files for subsequent analysis.
* **Phase 2:** Involved scaling the project into a continuous monitoring scenario applied specifically to the clinical laboratory's refrigeration equipment. This phase required designing an architecture capable of acquiring data in real time, detecting out-of-range events, measuring exposure duration to non-permitted temperatures, and generating periodic traceability reports integrated into the quality management system. It also encompasses the online visualization of logged temperatures.
* **Phase 3:** Planned as a future enhancement, forecasts automated alert routing via email or mobile messaging to trigger contingency protocols for protecting biological products and vaccines within safe operational timeframes.

It is worth noting that for incubators, thermal control can be performed periodically as part of preventive maintenance schedules and according to applicable standards. For this reason, incubator thermal mapping can be managed locally without requiring continuous cloud integration. Conversely, monitoring refrigeration units that store biological products demands a continuous supervision model, thereby justifying a scalable cloud data architecture.

## Scaling the Project to Continuous Monitoring

The second phase of the project was directed toward transforming isolated thermal mapping into a continuous monitoring system capable of ingesting, validating, transforming, and analyzing temperature data from digital sensors connected to a cloud infrastructure.

To achieve this, a data architecture based on the Medallion pattern was deployed within the `my_storage_account` storage account by creating bronze, silver, and gold containers. This structure organizes data according to its processing maturity: raw data in the Bronze layer, validated and enriched data in the Silver layer, and finalized analytical reports in the Gold layer.

* **Bronze Layer:** Stores telemetry records in raw JSON format, partitioned or organized by generation date and time.
* **Silver Layer:** Defines dedicated directories for storing corrupted/rejected records, validated telemetry (stored as Delta tables), and sensor metadata. This metadata contains critical context such as sensor identifiers, locations, minimum/maximum temperature thresholds, and assigned personnel.
* **Gold Layer:** Consolidates detailed and aggregated analytical reports stored in Delta format.

### Data Ingestion and Simulation (Bronze Layer)

To validate the cloud architecture end-to-end without relying on physical hardware during the testing stage, an IoT telemetry simulator was developed in VS Code using Python. This simulator generates realistic thermal data based on the hospital's biomedical equipment profiles. The ingestion workflow operates as follows:

1. The Python script streams the simulated events directly into **Azure IoT Hub**.
2. **Azure Stream Analytics (ASA)** processes this continuous real-time stream.
3. The readings are transformed into raw JSON files and automatically stored in the **Bronze** layer within the Azure Blob Storage account container, organized by date and time of generation.

### Processing and Transformation (Silver & Gold Layers)

The extraction and processing stage was developed in **Azure Databricks**. From this environment, secure connectivity to the storage architecture was established to extract both the telemetry JSON files and the `sensores_limites.csv` metadata reference file.

During the transformation stage, JSON files were processed and converted into Delta tables. Simultaneously, telemetry streams were joined with the sensor metadata to enrich each record with its operational context. In this phase, strict data quality rules were applied, including:
* Validation of mandatory fields.
* Verification of timestamp formats.
* Exclusion of invalid hardware placeholder values (such as `999`).
* Isolation of unmapped sensors (telemetry records without matching metadata).

Invalid records were routed to an audit directory within the Silver layer for debugging, while valid data was promoted to a curated Delta table for validated telemetry. Subsequently, alert states (`BAJA`, `ALTA`, `OK`) and thermal deviations were computed by comparing measured temperatures against the thresholds declared in the metadata.

Finally, in the load stage, analytical reports were generated in the Gold layer, capturing metrics such as: monitored equipment type, location, sensor identifier, total readings processed, counts of in-range and out-of-range readings, average temperature, absolute minimum and maximum values observed, configured thresholds, and out-of-range event durations. These results were saved in Delta format, ensuring data traceability, versioning, and processing consistency.

## Pipeline Automation

Workflow automation was implemented via **Azure Data Factory (ADF)** through an orchestration pipeline that triggers the `Procesamiento_SenseTemp` notebook in Azure Databricks. This notebook encapsulates the entire extraction, validation, transformation, and reporting lifecycle.

For academic demonstration purposes and due to cloud resource budget constraints, a daily execution trigger was scheduled. This frequency suffices to demonstrate automated orchestration and seamless cross-service integration. However, in a production cold-chain monitoring scenario, execution should be near-real-time or event-driven based on data arrival, matching the operational criticality of the stored biomedical assets.

Online, real-time visualization of temperature curves remains projected as a future development phase to strengthen operational oversight and response times against critical thermal deviations.

## Challenges and Troubleshooting

Various technical and operational challenges were encountered during project development:

* **Cloud Migration:** Transitioning from a local standalone prototype to a scalable cloud architecture required integrating multiple distinct Azure and Databricks services into a single, cohesive automated workflow.
* **Medallion Design:** Structuring the Medallion architecture called for careful separation between raw, validated, and reporting data assets to maintain data auditability and processing clarity.
* **Data Quality Anomalies:** The transformation phase exposed data quality issues like invalid telemetry spikes, unmapped sensors, and potential schema drift. The validation logic had to be carefully adjusted to isolate actual anomalies without dropping viable data rows.
* **Delta Lake Scheme Conflicts:** Incompatibilities during Delta table append/write operations between successive notebook runs required isolating output paths and enforcing explicit schema constraints during execution.
* **ADF-Databricks Integration:** Configuring secure token authentication, establishing Linked Services, selecting proper cluster targets, and managing parameters within the scheduled trigger demanded precise orchestration setup.
* **Cost Optimization:** Balancing an ideal architecture design with the limited cloud credits available required adapting the execution frequency to an academic context without compromising the project's conceptual scalability for real-world deployment.

## Conclusions

The development of this project successfully demonstrated the feasibility of evolving a local temperature sensing prototype into an automated cloud data architecture tailored for critical hospital monitoring and asset traceability.

The integration of Azure IoT Hub, Azure Stream Analytics, Azure Storage, Azure Databricks, and Azure Data Factory established a robust pipeline for ingesting, validating, transforming, and reporting on biomedical equipment temperatures. The Medallion architecture provided optimal data separation, while Delta Lake ensured transactional consistency, scalability, and historical traceability.

Beyond addressing the core acquisition and processing needs, this solution lays a solid foundational framework for future capabilities such as incremental stream processing, instant alert notifications, and event-driven automation. Consequently, this project serves not only as a technical data engineering portfolio item but as a viable architecture pattern to enhance safety in high-stakes cold-chain environments.
