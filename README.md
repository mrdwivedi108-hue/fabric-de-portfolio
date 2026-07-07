# End-to-End Data Pipeline with Microsoft Fabric — Orders Data Processing

A small end-to-end data engineering pipeline built on Microsoft Fabric, covering ingestion, transformation, and orchestration. Built while preparing for the DP-700 (Fabric Data Engineer Associate) certification, as a hands-on complement to the exam material.

## Architecture

```
Public CSV (GitHub) 
   → Dataflow Gen2 (Power Query transformation) 
      → Lakehouse (Delta table) 
         → Pipeline (orchestration + scheduling trigger)
```

## What it does

1. **Ingestion**: Pulls order line-item data from a public CSV source.
2. **Transformation** (Dataflow Gen2 / Power Query):
   - Promotes the first row to column headers
   - Casts each column to its correct type (IDs to `Int64`, `OrderDate` to `date`, `LineItemTotal` to `number`)
   - Derives a new `MonthNo` column from `OrderDate` for downstream time-based analysis
3. **Storage**: Writes the transformed table to a Fabric Lakehouse as a managed Delta table.
4. **Orchestration**: A Fabric pipeline triggers the Dataflow refresh, with a configured retry policy and timeout, so the whole run is repeatable and schedulable rather than a manual one-off.

## Why these choices

- **Dataflow Gen2 over a Notebook** for this step: the transformation is straightforward column-level cleanup or reshaping, which Power Query's low-code interface handles well and keeps auditable/readable for less-technical stakeholders. A PySpark Notebook is the better choice once transformations get more complex (joins across large tables, custom aggregation logic) — planned for the next iteration of this project.
- **Lakehouse over Warehouse** as the destination: the Lakehouse's Delta format supports both structured queries via its SQL analytics endpoint and direct file-level access, giving flexibility for downstream consumption (Notebook, Power BI, or SQL).
- **Pipeline orchestration**: even a single-activity pipeline is included here deliberately, since orchestration (retry policy, scheduling, dependency handling) is a distinct skill from the transformation logic itself, and this project is meant to demonstrate both.

## Files in this repo

| File | Description |
|---|---|
| `dataflow/orders_transformation.m` | Power Query M code for the ingestion + transformation logic |
| `pipeline/pipeline_2.json` | Pipeline definition showing the orchestration activity and retry policy |
| `screenshots/` | Evidence of successful pipeline execution and resulting data in the Lakehouse |

## Status

This is an active, evolving project. Next planned additions:
- Incremental load logic (vs. current full refresh)
- A PySpark Notebook stage for more complex transformations
- A larger, more realistic dataset (fintech/transaction data) to better reflect production-scale scenarios
- Data quality validation checks before the Lakehouse write

## Author

Built by Priyaansh Dwivedi as part of a hands-on Data Engineering / AI Infrastructure portfolio, alongside DP-700 exam preparation.
