## Business Questions (Operational Analytics)

The semantic model is designed to answer the following questions:

- How are assets performing over time?
- Which assets experience the highest pressure or temperature variability?
- Are there early indicators of asset failure?
- Which locations experience the most frequent alarms?
- How do operational metrics trend hourly and daily?

## Fact Table: fact_telemetry_hourly

Source: gold_telemetry_hourly

Grain:
- One row per asset
- Per sensor category
- Per hour

Measures (Numeric Columns):
- avg_value
- max_value
- min_value

Foreign Keys:
- device_id
- asset_type
- location
- time (window_start)

### Dimension: dim_asset

Attributes:
- device_id (PK)
- asset_type
- location
- manufacturer
- installed_date

Purpose:
Provides business context for telemetry data.

### Dimension: dim_time

Attributes:
- datetime (PK)
- hour
- day
- month
- year

Purpose:
Supports time-based analysis and trending.

### Dimension: dim_sensor

Attributes:
- sensor_category (PK)
- description

Purpose:
Enables grouping and filtering by sensor type.

## Relationships

fact_telemetry_hourly.device_id → dim_asset.device_id
fact_telemetry_hourly.window_start → dim_time.datetime
fact_telemetry_hourly.sensor_category → dim_sensor.sensor_category

## Key Metrics (KPIs)

- Average Operating Value
- Peak Operating Value
- Variability (Max - Min)
- Asset Utilization Trend
- Alarm Frequency (future enhancement)






