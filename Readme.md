[![Releases](https://img.shields.io/badge/Releases-GitHub-blue?logo=github)](https://github.com/hosihung/Big-Data/releases)

# Big Data Architecture: Layers, Tools, Pipelines, Streaming & Batch Patterns

![Big Data Illustration](https://images.unsplash.com/photo-1531297484001-80022131f5a1?auto=format&fit=crop&w=1400&q=60)

Badges
- Topics: ![airflow](https://img.shields.io/badge/airflow-%23000000.svg?style=flat&logo=apache-airflow&logoColor=white) ![apache-spark](https://img.shields.io/badge/apache--spark-%2326432b.svg?style=flat&logo=apache-spark&logoColor=white) ![hadoop](https://img.shields.io/badge/hadoop-%23da532c.svg?style=flat&logo=apache-hadoop&logoColor=white) ![stream-processing](https://img.shields.io/badge/stream--processing-blue)

Quick links
- Releases (download and run the release assets): https://github.com/hosihung/Big-Data/releases
- Repo: https://github.com/hosihung/Big-Data

Table of contents
- Layers overview
- Layer details and patterns
- Tools matrix
- Example pipelines
- Local demo and releases
- Architecture diagrams
- Deployment options
- Contribution and license
- References

Layers overview

| Layer                    | Purpose                                                                                       | Common tools / remarks                                                                                          |
|--------------------------|-----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Data Ingestion/Streaming | Bring raw events and batches into the platform.                                               | Kafka, Kinesis, NiFi, Logstash. See DataIngestion.md.                                                          |
| Data Processing          | Transform, aggregate, enrich, and compute on data.                                            | Apache Spark, Flink, MapReduce, Storm, Kafka Streams. See DataProcessing.                                      |
| Data Cataloging          | Store schema, lineage, tags, and data contracts.                                               | Hive Metastore, AWS Glue, Apache Atlas.                                                                         |
| Data Storage             | Store raw, curated, and serving data.                                                          | HDFS, S3, ADLS, Delta Lake, Iceberg.                                                                            |
| Serving & Query          | Provide low-latency query and BI access.                                                       | Presto/Trino, Impala, Redshift, BigQuery, Looker.                                                               |
| Orchestration & Ops      | Schedule, monitor, deploy, and secure pipelines.                                               | Airflow, Argo, Kubernetes, Terraform.                                                                           |
| Observability            | Metrics, logs, tracing, and alerts.                                                            | Prometheus, Grafana, ELK, OpenTelemetry.                                                                        |

Layer details and patterns

Data Ingestion / Streaming
- Use event-driven ingestion for real-time needs and batch ingestion for bulk loads.
- Patterns: Daemon consumers, Kafka Connect, Change Data Capture (CDC).
- Best practice: Keep ingestion idempotent and assign durable offsets.
- Tools: Kafka, Confluent Platform, AWS Kinesis, Apache NiFi, Logstash.
- Files and scripts: release assets include sample ingestion scripts. Download and run the included ingestion scripts to start demo producers.

Data Processing
- Use a layered processing model:
  - Raw zone: append-only, immutable files.
  - Cleansed zone: corrected and validated data.
  - Enriched zone: joined with reference data.
  - Serving zone: partitioned, optimized for query.
- Batch vs streaming:
  - Batch suits large-window ETL and historical reprocessing.
  - Streaming suits low-latency analytics and alerting.
- Engines comparison (short):
  - Spark: wide ecosystem, resilient distributed datasets, good for batch + structured streaming.
  - Flink: strong streaming semantics, low latency, stateful processing.
  - MapReduce: legacy batch, fault-tolerant, higher latency.
  - Storm: low-latency stream processing, older ecosystem.
  - Kafka Streams: tightly integrated with Kafka, embeddable stream apps.
- See DataProcessing/SparkVsMapReduceVsFlinkVsStorm.md for a deep comparison.

Data Cataloging
- Register tables, schemas, and lineage.
- Enforce contracts via schema registry.
- Use automated scans to detect schema drift.
- Tools: Hive Metastore, AWS Glue Catalog, Apache Atlas, Confluent Schema Registry.

Data Storage
- Choose storage by access pattern:
  - Object stores (S3, ADLS) for cost-efficient long-term storage.
  - HDFS for on-prem high-throughput access.
  - Delta Lake / Iceberg for ACID and time travel.
- Partition by date or business key to limit scan scopes.
- Compaction and vacuum tasks reduce small-file overhead.

Serving & Query
- Use specialized engines for different queries:
  - Interactive BI: Presto/Trino, Redshift, BigQuery.
  - OLAP cubes: ClickHouse, Druid.
  - Ad-hoc SQL: Spark SQL, Hive.
- Provide materialized views for heavy aggregations.
- Use query profiling to identify expensive scans.

Orchestration & Ops
- Orchestrate via DAGs with Airflow or Argo.
- Store configs in Git and apply IaC.
- Use containers and Kubernetes for reproducible runtime.
- Monitor job success, lag, and runtime.

Observability
- Track pipeline metrics: throughput, latency, errors.
- Store logs centrally and attach traces to pipeline runs.
- Set alerts for backpressure, consumer lag, or job failures.

Tools matrix

- Ingestion: Kafka | NiFi | Logstash | Filebeat
- Processing: Spark | Flink | MapReduce | Kafka Streams
- Storage: HDFS | S3 | Delta Lake | Iceberg
- Catalog: Glue | Hive Metastore | Atlas
- Serving: Trino | Redshift | Druid | ClickHouse
- Orchestration: Airflow | Argo | Kubernetes
- Monitoring: Prometheus | Grafana | ELK

Example pipelines

1) Batch ETL with Spark (concept)
- Read raw parquet from S3.
- Validate with schema registry.
- Join with master data.
- Write partitioned Parquet to serving zone (Delta Lake).
- Sample Spark submit:
```bash
spark-submit \
  --master yarn \
  --deploy-mode cluster \
  --conf spark.sql.sources.partitionOverwriteMode=dynamic \
  jobs/etl_job.py --input s3://data/raw --output s3://data/serving
```

2) Stream enrichment with Flink (concept)
- Consume from Kafka topic.
- Enrich using keyed state and join to reference store.
- Produce to enriched Kafka topic and write checkpointed snapshots to S3.
- Run as a Kubernetes deployment with state backend on object storage.

3) CDC to data lake pattern
- Use Debezium to capture changes from a relational DB.
- Push changes to Kafka.
- Consume changes and apply upserts to Delta Lake or Iceberg tables.

Local demo and releases

- Download and run the release artifacts from this page: https://github.com/hosihung/Big-Data/releases
- The releases page contains demo assets and scripts. Download the provided files and execute the installer or run scripts (for example install.sh or run_demo.sh) to deploy the local demo stack.
- Example local start (after download):
```bash
# Extract release
tar -xzf big-data-demo-v1.tar.gz
cd big-data-demo

# Start local stack (scripts are provided in the release)
./run_demo.sh
```
- If the link does not work, check the Releases section on the repo page and download the latest release artifacts.

Architecture diagrams

- Logical architecture: ingestion -> processing -> storage -> serving.
- Physical deployments:
  - Cloud: S3, EMR/Dataproc, MSK/Kafka, Glue/Atlas, Trino on EC2.
  - On-prem: HDFS, YARN, Kafka, Hive Metastore, Trino.
- Useful image (visual aid): ![Architecture](https://miro.medium.com/max/1400/1*4JDcQ0y89lciWZ0HhV3bLA.png)

Security and governance

- Secure transport: TLS for brokers and APIs.
- Data encryption: server-side for object stores; field-level encryption for PII.
- Access control: RBAC on query engines and storage policies.
- Lineage: capture via tool integrations and automated metadata harvesting.

Scaling and cost tips

- Use auto-scaling for compute clusters to match load.
- Use object stores with lifecycle rules to tier cold data.
- Prefer vectorized formats (Parquet/ORC) to cut I/O.
- Compress and partition to reduce query cost.

CI/CD and testing

- Store pipelines as code and run unit tests for transforms.
- Use integration tests with a small sample dataset.
- Deploy artifacts via GitHub Actions or Jenkins.
- Canary new pipelines and roll back on regression.

Common operational tasks

- Monitor consumer lag for Kafka topics.
- Compact small files and vacuum stale snapshots for Delta/Iceberg.
- Rotate keys and audit access logs.
- Reprocess via replay from raw zone or Kafka offset replay.

Repository layout (suggested)
- /docs
  - DataIngestion.md
  - DataProcessing/
    - SparkVsMapReduceVsFlinkVsStorm.md
  - Architecture.md
- /demos
  - run_demo.sh
  - install.sh
- /examples
  - spark_jobs/
  - flink_jobs/
- /scripts
  - deploy.sh
  - cleanup.sh

How to contribute
- Open an issue to propose a change.
- Fork the repo and add docs, diagrams, or scripts.
- Create a pull request with a clear description and tests for code additions.
- Follow the code style and document new tools in the Tools matrix.

Contact and references
- Releases: https://github.com/hosihung/Big-Data/releases
- Topics: airflow, apache-spark, batch-processing, big-data, data-analytics, data-engineering, data-lake, data-warehouse, hadoop, hld, looker, map-reduce, redshift, stream-processing
- Useful reads:
  - Apache Spark docs
  - Flink streaming guide
  - Delta Lake transactions
  - Kafka design patterns

License
- Check LICENSE file in the repo for terms and conditions.

Images and assets
- Use open images for diagrams or generate your own architecture diagrams and add them under /docs/assets.
- Include small sample datasets for demos and mark them as sample data.

Contribution checklist
- Update docs for new tools.
- Add tests for processing logic.
- Provide sample DAGs for orchestration.
- Add a release entry when publishing demo assets.