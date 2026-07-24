# Walmart Data Engineering Pipeline

A beginner-friendly but production-inspired **end-to-end data engineering project** that demonstrates how data can be ingested from operational systems, transformed through layered data models, validated with automated quality checks, and orchestrated using Apache Airflow.

This project was built to strengthen my understanding of modern data engineering concepts and to demonstrate practical experience with **Airflow, dbt, Databricks, PostgreSQL, AWS S3, Docker, and dimensional modeling**.

## Architecture

![Walmart Data Engineering Architecture](assets/walmart-data-engineering-architecture.png)

The architecture combines two ingestion patterns:

- **CDC ingestion** from PostgreSQL for incremental operational data.
- **File ingestion** from AWS S3 for batch-based source data.

The data is processed in Databricks, transformed and tested with dbt, and orchestrated end to end using Apache Airflow.

## Business Problem

Retail data commonly arrives from multiple systems and in different formats. Raw operational data is often not immediately suitable for reporting, analytics, or conversational SQL use cases.

This project creates a reliable pipeline that:

- Ingests database changes and source files.
- Standardizes and cleans raw data.
- Applies business transformations.
- Validates data quality at important stages.
- Builds analytics-ready fact and dimension models.
- Produces a simplified reporting layer for downstream consumption.

## End-to-End Data Flow

```text
PostgreSQL / AWS S3
        ↓
Incremental ingestion in Databricks
        ↓
dbt source freshness checks
        ↓
Silver technical models
        ↓
Data quality tests
        ↓
Silver business models
        ↓
Data quality tests
        ↓
Gold dimensions and fact tables
        ↓
Analytics-ready reporting layer
```

Apache Airflow controls the task sequence, triggers the Databricks ingestion job, runs dbt transformations, and prevents downstream tasks from continuing when an upstream step fails.

## Technology Stack

| Technology | Purpose |
|---|---|
| Apache Airflow | Workflow orchestration and task dependency management |
| dbt | SQL transformations, tests, source freshness, and snapshots |
| Databricks | Scalable ingestion and data processing |
| PostgreSQL | Operational source system and CDC source |
| AWS S3 | File-based source storage |
| Docker | Reproducible local Airflow environment |
| Redis | Message broker for Airflow CeleryExecutor |
| PostgreSQL for Airflow | Airflow metadata database |

## Data Modeling Approach

The dbt project follows a layered transformation pattern.

### Silver Technical Layer

The first transformation layer focuses on technical standardization:

- Column naming and data-type consistency
- Basic cleaning and normalization
- Duplicate and null handling
- Preparation of reusable source-aligned models

### Silver Business Layer

The second transformation layer applies business meaning:

- Business rules and calculations
- Joins between related entities
- Reusable business-level datasets
- Preparation for dimensional modeling

### Gold Layer

The Gold layer contains analytics-ready models:

- **Dimension tables** for descriptive business entities
- **Fact tables** for measurable business events
- **dbt snapshots** to preserve historical dimension changes
- A simplified reporting layer for analytics and SQL-based consumption

## Airflow Orchestration

The Airflow DAG runs the pipeline in the following order:

```text
ingest_cdc
    → clean_target
    → source_freshness
    → silver_technical
    → silver_technical_tests
    → silver_business
    → silver_business_tests
    → gold_ephemeral
    → gold_dimensions
    → gold_facts
```

This sequencing ensures that transformations only run after ingestion succeeds and that each major modeling layer passes its quality checks before the next layer begins.

## Data Quality and Reliability

The project includes multiple controls to improve pipeline reliability:

- Databricks job-status polling before downstream processing
- dbt source freshness checks
- Tests after the technical Silver layer
- Tests after the business Silver layer
- Airflow task dependencies and failure propagation
- dbt snapshots for historical tracking
- Cleanup of generated dbt artifacts before a new run

## Key Engineering Decisions

### Why Airflow?

Airflow makes dependencies explicit, provides task-level monitoring, and gives a clear view of pipeline failures and retries.

### Why dbt?

dbt keeps transformation logic modular and version controlled while providing built-in testing, documentation, lineage, freshness checks, and snapshots.

### Why separate Silver and Gold layers?

Layered modeling separates source cleaning from business logic and analytical modeling. This improves readability, testing, reuse, and maintainability.

### Why use both CDC and file ingestion?

Real data platforms often receive data through more than one ingestion pattern. Supporting both incremental database changes and batch files makes the project closer to a practical enterprise pipeline.

## Skills Demonstrated

Through this project, I practiced:

- Designing an end-to-end data pipeline
- Building and scheduling an Airflow DAG
- Triggering and monitoring Databricks jobs through an SDK
- Developing modular dbt models
- Implementing dbt tests and source freshness checks
- Using snapshots for slowly changing dimensions
- Building fact and dimension tables
- Working with CDC and file-based ingestion
- Containerizing an Airflow environment with Docker
- Troubleshooting dependencies across multiple pipeline stages

## Repository Structure

```text
.
├── airflow/
│   ├── dags/
│   │   └── orchestrate.py
│   ├── walmart_project/
│   │   ├── models/
│   │   ├── snapshots/
│   │   ├── dbt_project.yml
│   │   └── profiles.yml
│   ├── config/
│   ├── Dockerfile
│   ├── docker-compose.yaml
│   └── requirements.txt
├── assets/
│   └── walmart-data-engineering-architecture.png
└── README.md
```

## Acknowledgement

This project was created as a hands-on learning exercise to build practical data engineering skills and to understand how modern tools work together in an end-to-end pipeline.
