## Pipeline Architecture Overview

The data platform uses a layered pipeline architecture aligned to the
lakehouse medallion pattern.

Pipelines are designed to:
- Ingest operational telemetry data
- Transform and enrich data incrementally
- Produce analytics-ready Gold tables
- Support monitoring, retries, and failure isolation

## Pipeline 1: Ingest Raw Telemetry (Bronze)

Purpose:
Ingest raw operational data into the Bronze layer with minimal transformation.

Source:
- Machine telemetry CSV / IoT feed (batch simulated)

Activities:
- Copy activity (raw files → lakehouse)
- Schema inference
- Append-only load

Output Tables:
- bronze_telemetry
- bronze_assets
- bronze_maintenance

Trigger:
- Scheduled (hourly or daily)

Failure Handling:
- Retry on transient failures
- Log rejected records

## Pipeline 2: Transform & Enrich (Silver)

Purpose:
Clean and enrich telemetry data for reuse.

Dependencies:
- Pipeline 1 completion

Activities:
- Spark notebook execution
- Data quality checks
- Join telemetry with asset metadata

Output Tables:
- silver_telemetry_enriched

Trigger:
- On success of Pipeline 1

Failure Handling:
- Fail fast on schema mismatch
- Alert on null key thresholds

## Pipeline 3: Aggregate for Analytics (Gold)

Purpose:
Produce analytics-optimized fact tables.

Dependencies:
- Pipeline 2 completion

Activities:
- Spark aggregation
- Time windowing
- Write Gold fact table

Output Tables:
- gold_telemetry_hourly

Trigger:
- On success of Pipeline 2

Failure Handling:
- Validate row counts
- Prevent partial overwrites

## Pipeline Dependencies

Pipeline 1 (Bronze Ingestion)
        ↓
Pipeline 2 (Silver Transformation)
        ↓
Pipeline 3 (Gold Aggregation)

## Scheduling Strategy

- Bronze ingestion: Hourly
- Silver transformation: Triggered on Bronze success
- Gold aggregation: Triggered on Silver success

This ensures data freshness while preventing partial processing.

## Microsoft Fabric Mapping

| Concept | Fabric Component |
|------|------------------|
| Orchestration | Data Factory Pipelines |
| Transformation | Data Engineering Notebooks |
| Storage | OneLake / Lakehouse |
| Scheduling | Time-based triggers |
| Monitoring | Pipeline run history |
