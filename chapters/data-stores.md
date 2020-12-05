# Concepts

- categories
  - Persistent Data Store
  - Transient Data Store
    - SQS
    - SNS
  - Ephemeral Data Store
    - EC2 Instance Store
    - MemcacheD
- IOPS vs. Throughput
  - IOPS: I/O operations per second
  - Throughput
- consistency models
  - ACID
    - atomic
    - consistent
    - isolated
    - durable
  - BASE
    - basic availability - available even if stale
    - soft-state - not instantly consistent across stores
    - eventual consistency
    
# Amazon S3

- S3 is an Object Store
- maximum object size is 5TB; largest object in a single put is 5GB
- recommend multi-part uploads if larger than 100MB
- object key looks like a path
- S3 consistency
  - read-after-write consistency for PUTs
  - GET before an object exists is eventual consistency
  - PUT and DELETE are eventual consistent
  - update to a single key is atomic
- access policies
  - Resource-based policies
    - object ACL
    - bucket policy
  - User-based policies
    - IAM policies
  - optional multi-factor authentication before __delete__
- versioning integrated with lifecycle management
- cross-region replication
  - security
  - compliance
  - latency
- storage classes
  - standard
  - standard IA
  - Reduced redundancy (after fetched from Glacier)
  - intelligent-tiering
  - glacier
- lifecycle rules
  - based on prefixes, tags, current version vs. previous version
- analytics ability
  - data lake
    - Athena
    - Redshift Spectrum
    - QuickSight
  - IoT Streaming data repository
    - Kinesis Firehose
  - Machine Learning and AI Storage
    - Rekognition
    - Lex
    - MXNet
  - Storage Class Analysis
    - S3 Management Analytics
- Encryption at rest
  - SSE-S3 with S3's key for AES-256
  - SSE-C with your own AES-256 key
  - SSE-KMS with key generated and managed by KMS
  - Client-side encryption using local encryption process before uploading to S3 (PGP, GPG)
- other nice features
  - transfer acceleration via cloudfront (and reverse)
  - requester __with AWS account__ pays for requests and data transfer instead of bucket owner
  - tags
  - events
  - static web hosting
  - BitTorrent support using BitTorrent protocol by automatically generating a .torrent file
  
# Amazon Glacier

- cheap, slow to respond, seldom accessed
- cold storage
- used by AWS Storage Gateway Virtual Tape Library
- integrated with S3 via Lifecycle Management
- Glacier vault (like a S3 bucket)
- Glacier archive
  - immutable
  - max size 40 TB
- Policy through Vault Lock
  - different than vault access policy
  - enforce rules like no deletes or MFA
  - immutable
  
# Elastic Block Storage

- virtual hard drive
- can only be used with EC2
- tied to a single AZ
- variety of optimized choice towards IOPS or throughput
- snapshots
  - cost-effective and easy backup strategy
  - share data 
  - migrate to new AZ or Region
  - convert unencrypted volume to an encrypted volume
  - snapshot is incremental using deltas
- EBS vs. instance store
  - instance store is temporary, and goes aways when EC2 is stopped or terminated
  - instance store has better performance
- Data lifecycle manager
  - schedule snapshots for volumes
  - retention rules to remove stale snapshots
  
# Amazon EFS

- implementation of NFS
- pay for what you use, elastic storage capacity
- multi-AZ metadata and data storage
- configure mount-points in one or many AZs
- can be mounted from on-premises systems, but
  - need fast internet access such as Direct Connect
  - no secure protection for connection, needs VPN
  - alternatively use Amazon DataSync to keep EFS and on-prem data store in sync securely
- EFS is about 3 times more expensive than EBS, and 20 times more expensive than S3

# Amazon Storage Gateway

- virtual machine run on-prem with VMWare or HyperV 
- provides local storage backed by AWS S3 and Glacier
- useful in cloud migrations: lazy and slow process to sync data over time
- modes
  - File Gateway
    - interface is NFS or SMB
  - Volume Gateway Stored Mode
    - inteface is iSCSI
  - Volume Gateway Cached Mode
    - inteface is iSCSI
  - Tape Gateway
    - iSCSI
- use case:
  - using Volume Gateway Cached Mode to sync data to remote offices 


# Amazon WorkDocs

- secure, fully managed file collaboration service
- integrate with AD for SSO
- web, mobile, and native clients (no linux)
- PIC ISO HIPAA compliance requirements

# Amazon Database on EC2

- run any database with full control
- good option for IBM DB2 and SAP HANA
- some third party tools that can be used to backup database to S3

# Amazon RDS

- managed database option for MySQL, Maria, PostgreSQL, SQL Server, Oracle and MySQL-compatible Aurora
- best for structured, relational data store needs
- drop-in replacement for existing on-prem instances of same databases
- automated backups and patching in customer-defined maintenance windows
- push-button scaling, replication, and redundancy
- anti-patterns
  - lots of large binary objects
  - automated scalability
  - name/value data structure
  - data is not well structured or unpredictable
  - other database platforms not supported by AWS
  - complete control over database
- Multi-AZ RDS (stand-by)
  - sync replication
- Read-replicas service other regional users
  - non-transactional storage engine MyISAM does not support replication
  - async replication
- fail-over
  - master fails -> standup is promoted to the master
  - whole region fails -> read replica promoted to stand-alone (single-AZ) -> single-AZ reconfigured to multi-AZ
  
  
# Amzaon DynamoDB
- managed, multi-AZ NoSQL with cross-region replication
- defaults to eventual consistency but can request strongly consistent read via SDK
- priced on throughput rather than compute
- provision read and write capacity in anticipation of need
- autoscale capacity
  - send in synthetic load for scaling in
  - on-demand capacity for flexible capacity at a small premium cost
- ACID-support with DynamoDB transactions
- Relational vs. NoSQL
- design best practices
  _frequently appear in the exame_
  - replicas via secondary indexes
    - e.g. premium customers and normal customers isolate W/R units
    
# Amazon Redshift

- fully managed, clustered peta-byte scale data warehouse
- postgreSQL compatible with JDBC and ODBC drivers
- features parallel processing and columnar data stores which are optimized for complex queries
- option to query directly from data files on S3 via Redshift Spectrum
- Data Lake
  - query raw data without extensive pre-processing
  - lessen the time from data collection to data value
  - identify correlations between disparate data sets
  - data in S3 <- redshift spectrum <- analytics tools

# Elasticache

- fully managed implementations of Redis and memcached
- push-button scalability for memory, writes and reads
- in memory key/value store- not persistent in the traditional sense
- billed by node-size and use time
- memcached vs. redis
  - memcached
    - simple, straight-forward
    - scale out/in as demand changes
  - redis
    - encryption
    - HIPAA compliance
    - clustering
    - complex data types
    - high-availability (replication)
    - pub/sub capability
    - geospatial indexing
    - backup and restore



# Amazon Neptune

- fully-managed graph database


# Amazon Athena
- SQL engine overlaid on S3 based on Presto
- query raw data objects as they sit in S3 bucket
- use or convert data to parquet to gain performance jump

# Amazon Quantum Ledger Database

- based on blockchain concepts
- provides immutable and transparent journal as a service
- centralized design
- append-only concept

# Amazon Managed Blockchain

- fully managed blockchain framework supporting Hyperledger Fabric and Ethereum
- distributed consensus-based 
- use the Amaon QLDB under-the-hood

# Amazon Timestream Database

# Amazon DocumentDB
- with MongoDB compatibility
- fully managed with multi-AZ HA, scalable, integrated with KMS, and backed up to S3
- migrate from MongoDB easily

# Amazon ElasticSearch
- ELK stack
  - Analytics: Kibana 
  - Intake: Logstash, cloudwatch, firehose, IoT 
  - Search and storage: ElasticSearch
- overlap with __AWS Quicksight__?


# References

- [AWS Storage Services Overview](https://d1.awsstatic.com/whitepapers/Storage/AWS%20Storage%20Services%20Whitepaper-v9.pdf)
- [SaaS Storage Strategies for multitenant](https://d1.awsstatic.com/whitepapers/Multi_Tenant_SaaS_Storage_Strategies.pdf)
- [Performance at Scale with Amazon ElasticCache](https://d0.awsstatic.com/whitepapers/performance-at-scale-with-amazon-elasticache.pdf)



