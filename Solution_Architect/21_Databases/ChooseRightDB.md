# Choosing the Right Database

Questions to choose the right databsse based on your architecture

  - Read-heavy, write-heavy, or balanced workloads ?
  - Throughput needs? Will it change, does it need to scale or fluctuate during the day?
  - How much data to store and for how long? Will it grow? Average object size? How are they accessed?
  - Data durability? Source of truth for the data?
  - Latency requirements? Consurrent users?
  - Data model? How will you query the data? Joins? Structured? Semi-structures?
  - Strong schema? More flexibility? Reporting? Search? RDBMS / NoSQL?
  - License costs? Switch to Cloud Native DB such as Aurora?

- **Database Types**

    - **RDBMS** (= SQL / OLTP): RDS, Aurora — great for joins
    - **NoSQL database** — no joins, no SQL: DynamoDB (~JSON), ElastiCache (Key / value pairs), Neptune (graphs), Document DB (for MongoDB), Keyspaces (for Apache Cassandra)
    - **Object Store**: S3 (for big objects) / Glacier (for backup / archives)
    - **Data Warehouse** (= SQL Analytics / BI): Redshift (OLAP), Athena, EMR
    - **Search**: OpenSearch (JSON) — free text, unstructured searches
    - **Graphs**: Amazon Neptune — displays relationships between data
    - **Ledger**: Amazon Quantum Ledger Database
    - **Time Series**: Amazon Timestream 


- **RDS**
    - Managed PostgreSQL / MySQL / Oracle / SQL Server / MariaDB / Custom
    - Provisioned RDS Instance size and EBS Volume Type & Size
    - Auto-scaling capability for storage
    - Support for read replicas and Multi-AZ
    - Security through IAM, Security Groups, KMS, SSL intransit
    - Automated Backup with Point in time restore feature (upto 35 days) (Restore will create a new database)
    - Manual DB Snapshot for longer-term recovery
    - Managed and Scheduled maintenance (with downtime)
    - Support for IAM Authentication, integration with Secrets Manager
    - RDS Custom for access to and customize the underlying instance (Oracle and SQL Server)

    - **Use Case**: Store relational datasets (RDBMS/OLTP), perform SQL queries, transactions

- **Aurora**
    - Compatible API for PostgreSQL / MySQL, separation of storage and compute.
    - 

- **ElastiCache**

- **DynamoDB**

- **S3**

- **DocumentDB**

- **Neptune**

- **Keyspaces (for Apache Cassandra)**

- **QLDB**

- **Timestream**
