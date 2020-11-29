# Migration Planning

## Migration Strategies

- Re-Host: "Lift and Shift"
  - move assets with no change
  - e.g. move on-prem MYSQL database to EC2 instance
- Re-platform: "Lift and Reshape"
  - move assets and also change underlying platform
  - e.g. move on-prem MYSQL database to RDS MYSQL
- Re-Purchase: "Drop and Shop"
  - abandon existing and purchase new
  - e.g. migrate on-prem CRM to salesforce.com
- Rearchitect
  - redesign in cloud-native manner
  - e.g. create serverless version of legacy application
- Retire
  - get rid of applications
- Retain
  - do nothing...
  
## Cloud Adoption Framework

- The Open Group Architectural Framework (TOGAF)

### Typical Cloud Adoption Phases

1. Project
  - running projects to experience benefits from the cloud
2. Foundation
  - creating landing zone(s), i.e. pre-configured, secure, multi-account AWS environment
  - cloud center of excellence (CCoE)
  - operations model
  - security and compliance readiness
3. Migration
4. Reinvention
  - retire tech. debt
  - use cloud-native behavior and functionality
  
### Components
- business
- people
  - traning options
- governance
- platform
  - standardization
- security
  - identity and access
  - logging and audit
  - shared responsibility model
- operations
  - business continuity and disaster recovery

## Hybrid Architectures

- common first step as pilot for cloud migrations
- ideally, integrations are loosely coupled
- example
  - AWS Storage Gateway creating a bridge between on-prem and AWS
    - transparent
    - common first step
  - ERP writing to SQS via middlewares, triggering update to DynamoDB
    - middleware is a common way to leverage cloud services
    - loosely-coupled, canonical-based
  - VMWare vCenter plugin allows transparent migration of VMs to and from AWS
  
## Migration tools

### Storage Migration Service
- AWS Storage Gateway
- AWS Snowball

### Server Migration Serivce
- VMWare vSphere
- repicates VMs to AWS, syncing volumes and creating periodic AMIs

### Database Migration Serivce

- Schema Conversion Tool (SCT)
- SCT copy schemas for homogeneous migrations or convert schemas for heterogeneous migrations
- SCT is used for larger migrations like data warehouses
- DMS also supports simpler conversions for MongoDB and DynamoDB
- DMS has replication function for on-prem to AWS or to Snowball or S3
![image](https://user-images.githubusercontent.com/60513695/100541916-0e2e8880-3282-11eb-80b8-658322b38ed2.png)

### Application Discovery Service

- collects config, usage, and behavior data from your on-prem. servers to help in cloud migration planning
- estimate TCO
- run as agent-less (VMWare environment) or agent-based (non-VMWare environment)
- only supports Linux and Windows
- accessed from AWS Migration Hub

## Network Migrations and Cutover

- CIDR reservations
  - ensure IP ranges will not overlap between VPC and on-prem.
  - VPCs support IPv4 range from /16 to /28; 5 IPs are reserved in every VPC
- journey
  - start with a VPN connection
  - add Direct Connect but keep VPN as a backup
  - transition from VPN to Direct Connect is seamless using BGP
  
## Snow Family

- import/export
- encrpted at-rest / in-transit
- AWS Snowball 80T NAS in a box
- AWS Snowball Edge, same as Snowball, but with onboard Lambda
- AWS Snowmobile up to 100P


# Exam Tips

- migration strategies and tradeoffs
- cloud adoption framework
- hybrid architectures
- migrations
- network migration and cutover


# References

- [AWS Migration Whitepaper](https://d1.awsstatic.com/whitepapers/Migration/aws-migration-whitepaper.pdf)
- [AWS Cloud Adoption Framework](https://d1.awsstatic.com/whitepapers/aws_cloud_adoption_framework.pdf)
- [Migrating Applications with RDS](https://d1.awsstatic.com/whitepapers/Migration/migrating-applications-to-aws.pdf)
- [AWS Cloud Transformation Maturity Model](https://d1.awsstatic.com/whitepapers/AWS-Cloud-Transformation-Maturity-Model.pdf)
  
