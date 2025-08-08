# Extract-Transform-Load (ETL)

|           | Remarks                                                                                                                                                                                      |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| EXTRACT   | EXTRACT data from multiple different data sources like [OLTP-DBs](https://github.com/Anshul619/HLD-System-Designs/tree/main/1_Databases/OLTPvsOTAP.md), third-party-sources etc.             |
| TRANSFORM | TRANSFORM data by de-duplicating it, combining it, ensuring quality & best suited for analytics.                                                                                             |
| LOAD      | LOAD data into one, centralized [OLAP db](https://github.com/Anshul619/HLD-System-Designs/tree/main/1_Databases/OLTPvsOTAP.md) like [data warehouses](DataStorage/DataWarehouses/Readme.md). |

[Read more](https://aws.amazon.com/what-is/etl/)

# ETL Techniques
- [Change Data Capture](https://github.com/Anshul619/HLD-System-Designs/tree/main/1_Databases/5_Database-Internals/ChangeDataCapture/Readme.md)