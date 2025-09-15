# Ajantha Pharma's ETL Pipeline

This repository contains documentation and resources for a robust, incremental ETL pipeline built by **Sriman Satwik Reddy Chinnam** & **Yatin Kumar Reddy K** (Mahindra University, Hyderabad, July–August 2025). The system utilizes **Airbyte** for Change Data Capture (CDC)-based extraction from PostgreSQL tables and **Apache NiFi** for automated batch processing and UPSERT operations into a target database.

## Project Highlights

- **Incremental Data Sync**: Uses Airbyte CDC (WAL) for reliable detection and extraction of new/changed data.
- **Dual Storage**: Raw data batches written to PostgreSQL staging schema and immutable object storage.
- **Automated Workflow**: Airbyte triggers NiFi via webhooks for downstream batch UPSERTs, supporting efficient replay.
- **Stateful Processing**: NiFi tracks synchronization state using Airbyte metadata columns.

## System Architecture

- **PostgreSQL Source**: Master and transaction tables; indexes & permissions secured for ETL flow.
- **Airbyte**: Periodic CDC sync; configuration tuned for large initial loads and incremental micro-batches.
- **Object Storage**: Raw batch copy per `stream/date/hour` for replay/audit.
- **Apache NiFi**: Daily task reads only new records since last sync, mapping and UPSERTing into target table using _airbyte_extracted_at as the cursor.
- **Coordination**: Scheduling ensures smooth handoff; logs and schema validation included.

## Quickstart Setup

1. **Install Dependencies**:
   - Airbyte OSS ([Quickstart guide](https://docs.airbyte.com/platform/using-airbyte/getting-started/oss-quickstart))
   - Docker (required for Airbyte)
   - Apache NiFi 2.4.0+ and PostgreSQL JDBC driver (`postgresql-42.7.3.jar`)

2. **Configure PostgreSQL**:
   - Create source (`scms_data_2`) and target (`scms_data_1`) tables with Airbyte metadata columns.
   - Create user `nifi_user`, grant necessary permissions.

3. **Airbyte Setup**:
   - Connect Airbyte to PostgreSQL with CDC (WAL) mode, set replication slot/publication.
   - Configure destination and schedule sync (every 6 hours).

4. **NiFi Pipeline**:
   - Use `QueryDatabaseTableRecord` (incremental by `_airbyte_extracted_at`) and `PutDatabaseRecord` (UPSERT by `id`) processors.
   - Validate controller services (DBCPConnectionPool, AvroRecordSetWriter/Reader), align schedule (daily at 19:00).

## Documentation

- **Airbyte/PostgreSQL Ingestion**: See Airbyte_Documentation.docx
- **Apache NiFi ETL & Integration**: See Apache_NiFi_Documentation.docx

Includes troubleshooting, sample DDL, performance tuning, security recommendations, and operational guidance.

---

**For details, best practices, and advanced troubleshooting, refer to the attached documentation files.**

---

## Authors

Sriman Satwik Reddy Chinnam
Yatin Kumar Reddy K  

Mahindra University, Hyderabad  
Internship Project (July–August 2025)
