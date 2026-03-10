---
description: Integration libraries index for 40+ tools and technologies (dbt, Fivetran, Snowflake, AWS, etc.).
type: index
triggers:
  - "integration, external tool, dagster-\\*"
  - "dbt, fivetran, airbyte, snowflake, bigquery, sling, aws, gcp"
---

# Integrations Reference

Dagster provides integration libraries for a range of tools and technologies. This reference directory contains detailed information about specific integrations.

Integration libraries are typically named `dagster-<technology>`, where `<technology>` is the name of the tool or technology being integrated. Integrations marked as _(community)_ are maintained by the community rather than the Dagster team.

All integration reference files contain a link to the official documentation for the integration library, which can be referenced in cases where the local documentation does not provide sufficient information.

## Reference Files Index

<!-- BEGIN GENERATED INDEX -->

- [dagster-airbyte](./dagster-airbyte/INDEX.md) — Integration with Airbyte for EL (Extract-Load) syncs as Dagster assets. (airbyte, extract-load sync)
- [dagster-airflow](./dagster-airflow/INDEX.md) — Integration with Apache Airflow for running Dagster pipelines in Airflow. (airflow, apache airflow, DAG)
- [dagster-airlift](./dagster-airlift/INDEX.md) — Integration with Airflow for migrating and co-orchestrating Airflow DAGs. (airflow, airlift, DAG migration)
- [dagster-aws](./dagster-aws/INDEX.md) — Integration with AWS services (S3, ECS, Lambda, etc.). (aws, s3, ecs, lambda, amazon)
- [dagster-azure](./dagster-azure/INDEX.md) — Integration with Azure services (ADLS, Blob Storage, etc.). (azure, adls, blob storage, microsoft cloud)
- [dagster-celery](./dagster-celery/INDEX.md) — Integration with Celery for distributed task execution. (celery, distributed task queue)
- [dagster-celery-docker](./dagster-celery-docker/INDEX.md) — Integration with Celery and Docker for distributed containerized execution. (celery docker, distributed container execution)
- [dagster-celery-k8s](./dagster-celery-k8s/INDEX.md) — Integration with Celery and Kubernetes for distributed container orchestration. (celery kubernetes, celery k8s, distributed orchestration)
- [dagster-census](./dagster-census/INDEX.md) — Integration with Census for reverse ETL syncs (community-maintained). (census, reverse ETL)
- [dagster-dask](./dagster-dask/INDEX.md) — Integration with Dask for parallel and distributed computing. (dask, parallel computing)
- [dagster-databricks](./dagster-databricks/INDEX.md) — Integration with Databricks for Spark-based data processing. (databricks, spark cluster)
- [dagster-datadog](./dagster-datadog/INDEX.md) — Integration with Datadog for monitoring and observability. (datadog, monitoring, observability)
- [dagster-datahub](./dagster-datahub/INDEX.md) — Integration with DataHub for metadata management and data cataloging. (datahub, metadata, data catalog)
- [dagster-dbt](./dagster-dbt/INDEX.md) — Integration with dbt Core and dbt Cloud for model-level orchestration. (dbt, dbt Core, dbt Cloud; dagster-dbt, dbt model, dbt project)
- [dagster-deltalake](./dagster-deltalake/INDEX.md) — Integration with Delta Lake for lakehouse storage (community-maintained). (delta lake, lakehouse)
- [dagster-deltalake-pandas](./dagster-deltalake-pandas/INDEX.md) — Integration with Delta Lake and Pandas for DataFrame IO managers. (delta lake pandas, deltalake dataframe)
- [dagster-deltalake-polars](./dagster-deltalake-polars/INDEX.md) — Integration with Delta Lake and Polars for DataFrame IO managers. (delta lake polars, deltalake dataframe)
- [dagster-dlt](./dagster-dlt/INDEX.md) — Integration with dlt (data load tool) for declarative data pipelines. (dlt, data load tool, declarative pipelines)
- [dagster-docker](./dagster-docker/INDEX.md) — Integration with Docker for containerized execution. (docker, container execution)
- [dagster-duckdb](./dagster-duckdb/INDEX.md) — Integration with DuckDB for in-process analytical queries. (duckdb, analytical database)
- [dagster-duckdb-pandas](./dagster-duckdb-pandas/INDEX.md) — Integration with DuckDB and Pandas for DataFrame IO managers. (duckdb pandas, duckdb dataframe)
- [dagster-duckdb-polars](./dagster-duckdb-polars/INDEX.md) — Integration with DuckDB and Polars for DataFrame IO managers. (duckdb polars, duckdb dataframe)
- [dagster-duckdb-pyspark](./dagster-duckdb-pyspark/INDEX.md) — Integration with DuckDB and PySpark for DataFrame IO managers. (duckdb pyspark, duckdb spark)
- [dagster-embedded-elt](./dagster-embedded-elt/INDEX.md) — Integration for embedded ELT with Sling and dlt support. (embedded elt, sling, dlt, extract load transform)
- [dagster-fivetran](./dagster-fivetran/INDEX.md) — Integration with Fivetran for managed EL (Extract-Load) connectors. (fivetran, managed connectors, extract-load)
- [dagster-gcp](./dagster-gcp/INDEX.md) — Integration with Google Cloud Platform (BigQuery, GCS, etc.). (gcp, bigquery, gcs, google cloud)
- [dagster-gcp-pandas](./dagster-gcp-pandas/INDEX.md) — Integration with GCP BigQuery and Pandas for DataFrame IO managers. (gcp pandas, bigquery pandas, bigquery dataframe)
- [dagster-gcp-pyspark](./dagster-gcp-pyspark/INDEX.md) — Integration with GCP BigQuery and PySpark for DataFrame IO managers. (gcp pyspark, bigquery pyspark, bigquery spark)
- [dagster-github](./dagster-github/INDEX.md) — Integration with GitHub for repository event handling. (github, repository events)
- [dagster-great-expectations](./dagster-great-expectations/INDEX.md) — Integration with Great Expectations for data validation and testing. (great expectations, data validation)
- [dagster-hightouch](./dagster-hightouch/INDEX.md) — Integration with Hightouch for reverse ETL and data activation. (hightouch, reverse ETL, data activation)
- [dagster-iceberg](./dagster-iceberg/INDEX.md) — Integration with Apache Iceberg for table format management (community-maintained). (iceberg, table format)
- [dagster-jupyter](./dagster-jupyter/INDEX.md) — Integration with Jupyter for notebook-based assets. (jupyter, notebook asset)
- [dagster-k8s](./dagster-k8s/INDEX.md) — Integration with Kubernetes for container orchestration and execution. (kubernetes, k8s, container orchestration)
- [dagster-looker](./dagster-looker/INDEX.md) — Integration with Looker for BI dashboard assets. (looker, BI dashboard)
- [dagster-mlflow](./dagster-mlflow/INDEX.md) — Integration with MLflow for ML experiment tracking and model management. (mlflow, ML tracking, model management)
- [dagster-msteams](./dagster-msteams/INDEX.md) — Integration with Microsoft Teams for notifications and alerts. (microsoft teams, msteams, notifications)
- [dagster-mysql](./dagster-mysql/INDEX.md) — Integration with MySQL for database storage backends. (mysql, database backend)
- [dagster-omni](./dagster-omni/INDEX.md) — Integration with Omni for analytics and BI. (omni, analytics)
- [dagster-openai](./dagster-openai/INDEX.md) — Integration with OpenAI for LLM-powered assets. (openai, LLM, AI integration)
- [dagster-pagerduty](./dagster-pagerduty/INDEX.md) — Integration with PagerDuty for incident management alerts. (pagerduty, incident management, alerting)
- [dagster-pandas](./dagster-pandas/INDEX.md) — Integration with Pandas for DataFrame type checking and validation. (pandas, dataframe validation)
- [dagster-pandera](./dagster-pandera/INDEX.md) — Integration with Pandera for DataFrame schema validation. (pandera, schema validation)
- [dagster-papertrail](./dagster-papertrail/INDEX.md) — Integration with Papertrail for log management. (papertrail, log management)
- [dagster-polars](./dagster-polars/INDEX.md) — Integration with Polars for fast DataFrame processing (community-maintained). (polars, fast dataframe)
- [dagster-polytomic](./dagster-polytomic/INDEX.md) — Integration with Polytomic for data syncing. (polytomic, data sync)
- [dagster-postgres](./dagster-postgres/INDEX.md) — Integration with PostgreSQL for database storage backends. (postgres, postgresql, database backend)
- [dagster-powerbi](./dagster-powerbi/INDEX.md) — Integration with Power BI for BI dashboard assets. (power bi, powerbi, BI dashboard)
- [dagster-prometheus](./dagster-prometheus/INDEX.md) — Integration with Prometheus for metrics collection. (prometheus, metrics)
- [dagster-pyspark](./dagster-pyspark/INDEX.md) — Integration with PySpark for distributed data processing. (pyspark, spark, distributed processing)
- [dagster-sigma](./dagster-sigma/INDEX.md) — Integration with Sigma for BI and analytics assets. (sigma, BI analytics)
- [dagster-slack](./dagster-slack/INDEX.md) — Integration with Slack for notifications and alerts. (slack, notifications, chat alerts)
- [dagster-sling](./dagster-sling/INDEX.md) — Integration with Sling for EL (Extract-Load) data replication. (sling, data replication, extract-load)
- [dagster-snowflake](./dagster-snowflake/INDEX.md) — Integration with Snowflake for cloud data warehouse operations. (snowflake, cloud data warehouse)
- [dagster-snowflake-pandas](./dagster-snowflake-pandas/INDEX.md) — Integration with Snowflake and Pandas for DataFrame IO managers. (snowflake pandas, snowflake dataframe)
- [dagster-snowflake-polars](./dagster-snowflake-polars/INDEX.md) — Integration with Snowflake and Polars for DataFrame IO managers. (snowflake polars, snowflake dataframe)
- [dagster-snowflake-pyspark](./dagster-snowflake-pyspark/INDEX.md) — Integration with Snowflake and PySpark for DataFrame IO managers. (snowflake pyspark, snowflake spark)
- [dagster-spark](./dagster-spark/INDEX.md) — Integration with Apache Spark for distributed data processing. (spark, distributed processing)
- [dagster-ssh](./dagster-ssh/INDEX.md) — Integration with SSH for remote command execution. (ssh, remote execution)
- [dagster-tableau](./dagster-tableau/INDEX.md) — Integration with Tableau for BI dashboard assets. (tableau, BI dashboard)
- [dagster-twilio](./dagster-twilio/INDEX.md) — Integration with Twilio for SMS and communication. (twilio, SMS, communication)
- [dagster-wandb](./dagster-wandb/INDEX.md) — Integration with Weights and Biases for ML experiment tracking (community-maintained). (wandb, weights and biases, ML tracking)
<!-- END GENERATED INDEX -->
