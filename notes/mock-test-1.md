# Migration Planning 

- [Application & Database migration service](https://jayendrapatil.com/aws-cloud-migration-services/)
  - AWS EC2 VM Import/Export
  - AWS Server Migration Service (SMS) 
  - AWS Database Migration Service (DMS)
- Oracle RAC (real application cluster) is not supported by AWS RDS
- to support client-side certificates, ELB shouldn't terminate the SSL, so its listener protocol must be TCP
- SNS invokes Lambda asynchronously, SQS invokes Lambda synchronously via event source mappings
- SNS has retries, and DLQ per subscription
- SNS supports HTTPS subscribers, but requires trusted CA-signed certificates
- Fargate abstracts away the resource provision, so we cannot specify spot instances
- AWS batch supports a list of jobs via array job and can specify their dependencies; AWS batch can also specify the computing resources such as a fleet of spot instances
- iSCSI is SAN compatible, so are the Storage Gateways
- SMB is similar to NFS, but is natively supported by Windows. current support for SMB includes Storage Gateway File gateway, and [Amazon FSx for Windows File Server](https://aws.amazon.com/fsx/windows/faqs/). For migration, should use AWS DataSync, or Windows's RoboCopy
  - DataSync can copy data in NFS, SMB, self-managed object storage, AWS Snowcone, S3 bucket, EFS, and Amazon FSx for Windows
