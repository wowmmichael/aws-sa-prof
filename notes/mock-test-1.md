# Migration Planning 

- [Application & Database migration service](https://jayendrapatil.com/aws-cloud-migration-services/)
  - AWS EC2 VM Import/Export
    - import vm image to EC2 and export back to on-prem. env
    - leverage existing investment in virtual machines
  - AWS Server Migration Service (SMS)
    - agentless service for migrating on-prem workload to AWS
    - incremental replications of live server volumes
    - supports VMWare vSphere, Windows Hyper-V, Azure VM
    - support all kinds of OS
    - save server volume as new AMI
    - used for _re-host_    
  - AWS Database Migration Service (DMS)
    - source database remains fully operational
    - supports homogeneous migration, e.g. Oracle to Oracle, and heterogeneous migration, e.g. Oracle to Aurora
    - supports both one-time data migration into RDS and EC2-based database as well as continuous data replication
    - supports continuous replication of the data into Amazon Redshift and Amazon S3
    - provides free AWS Schema Conversion Tool (SCT) to convert Oracle/SQL Server tables into Aurora/PostgreSQL tables
  
