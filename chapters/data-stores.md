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


  

