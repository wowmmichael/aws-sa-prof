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
- Lift-and-shift hints: install DB2 on an EC2 instance, replace IBM MQ with Amazon MQ, VM import/export to EC2


# Design for New Solutions

- DynamoDB used to get throttled by having too many paritions due to more data, and each partition share the throughput evenly. So in question, archiving old data and rebuild table can reduce the number of partitions, and prevent throttling. But this may no longer be relevant due to [instant adaptive capacity](https://aws.amazon.com/about-aws/whats-new/2019/05/amazon-dynamodb-adaptive-capacity-is-now-instant/)
- Big Data & Machine Learning
  - Glue ML Transforms - FindMatches with machine learning, useful for deduplication data in data lake
  - Kinesis Data Analytics - SQL manipulation of the incoming stream
  - SageMaker ... Caffee framework is commonly used for computer vision
- Lambda processing limit is 15 minutes
- SQS message size limit is 2GB, Kinesis message size limit is 1MB
- instance store-backed AMI vs EBS-backed AMI
- [Lambda@Edge and content-based dynamic origin selection for cloudfront](https://aws.amazon.com/about-aws/whats-new/2017/11/lambda-at-edge-now-supports-content-based-dynamic-origin-selection-network-calls-from-viewer-events-and-advanced-response-generation/)
- Dynao
- [Lambda@Edge and content-based dynamic origin selection for cloudfront](https://aws.amazon.com/about-aws/whats-new/2017/11/lambda-at-edge-now-supports-content-based-dynamic-origin-selection-network-calls-from-viewer-events-and-advanced-response-generation)
- DynamoDB autoscaling with CloudWatch is effective in [reducing cost](https://aws.amazon.com/blogs/database/amazon-dynamodb-auto-scaling-performance-and-cost-optimization-at-any-scale/#:~:text=To%20configure%20auto%20scaling%20in,alarms%20that%20track%20consumed%20capacity), but to support real spiky load with no delay at all, __on-demand capacity mode__ is needed
- VMWare Cloud on AWS sometimes provide the easiest and highly available migration solution, especially the on-prem is already on VMWare ESXi and using Oracle RAC
- [AWS Session Manager is much better than Bastion Host](https://aws.amazon.com/blogs/mt/replacing-a-bastion-host-with-amazon-ec2-systems-manager/)
- Lambda requies free IP in the VPC to run
- In VPC, service endpoint for S3 and DynamoDB (called Gateway endpoint) cannot be accessed from a second VPC via VPC-peering. But others called Interface Endpoint or PrivateLink can.
- Service endpoint can have endpoint policy to control access to the resource from VPC. For interface endpoints, security group can be defined for the endpoint network interface. Security group does not apply to Gateway Load Balancer endpoints [ref](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html)
- use AWS System Manager Patch Manager to manage patching a large group of EC2 or on-prem. instances
- AWS Step Function is recommended over Simple Workflow Service even human action is involved
  > Create a State Machine using Step Functions and a Lambda function for calling the API. Intake the claims into an S3 bucket configured with a CloudWatch Event. Trigger the Step Function from the CloudWatch Event. Create an Activity Task after the API check to email an unapproved claim to a human.
- [AWS IOT Core](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html) and MQTT (Message Queue Telemetry Transport)
- EMR do not store any HDFS data. Task node storage is ephermeral
- KMS Grant is used to allow access to the key programmatically. 
- Never be tempted to use _aws:SourceIp_ condition in IAM to control access from VPC, because VPC CIDR is virtual and can be overlapping.
- Operating CodePipeline, CodeCommit, CodeBuild, CodeDeploy from a central DevOps account is preferred
- [Lightsail](https://medium.com/@kyawzinlatt/aws-elastic-beanstalk-or-aws-lightsail-when-to-use-which-f448e4a49147), simplified Beanstalk
- Task Roles & Instance Roles for ECS
- EFS mount point uses a security group to control the access from EC2 instances. Use the EC2 SG group name as the source in the rule.


# Cost Control

- AWS Budgets should be used to track the reserved instances utilization. CloudWatch and Trusted Advisor do not provide that.
- Aurora Backtracking is the most cost-effective way to rewind operations on the same instance. Point-in-time restoration create new DB instance.
- subscribe to at least the Business Support Plan to gain access to the AWS Support API.
- on-demand capacity reservation vs. zonal reserved instance
- AWS Budget to notify budget violation (cost and forecast)
- chosse S3 storage class to fit the access pattern. Glacier with Expedited retrieval can also be considered if given 10-15 minutes access time and low access frequency.

# Continuous Improvement for Existing Solutions

- AWS Service catalog launch constraints and template constraint. role for launching resources is usually specified in the launch constraint
- RDS requires backups for manging read replica log and thus enabling read replica will disallow setting retention period to 0
- if you lose a private key for an EC2 key pair, use Systems Manager Automation or use a secondary instance to edit the authorized_keys file
- managing secret with AWS Parameter Store and Key Management Service appears for many times in the exam!
- Route53 private hosted zone for VPC
  - requies _enableDnsHostnames_ and _enableDnsSupport_
  - Route53 healthcheck is outside VPC. public IP address needs to be assigned to the instance in VPC
  - split-view DNS: same domain for public and private hosted zones
  - custom DNS server in VPC must configure to route private DNS queries to the IP of the Amazon-provided DNS Server in VPC, i.e. +2
- Route53 Geolocation routing policy best practice is to always specify a default option if the country cannot be identified
- ECS on hosted path is more cost effective than Fargate. Fargate is generally used for transient workload
- __AWS Artefacts__ for compliance document
- __ALB__ must have at least two subnets specified
- __AWS GuardDuty__ is a managed service that can watch CloudTrail, VPC Flow Logs and DNS Logs


# Design for Organizational Complexity

- AWS Directory Service For Microsoft Active Directory can configure a trust with on-premises AD, thus functionally covers AD Connect
- AWS Organization Service Control Policies
  - Deny List Mode vs. Allow List Mode (usually a bad idea)
  - SCP doesn't grant permissions. need to do that in IAM at the account level
  - (unrelated) trusted service & service linked role
- [IAM policy vs S3 Bucket Policy vs S3 ACL](https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources); bucket policy is used to allow public access; [offical recommendation on scenarios](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-policy-alternatives-guidelines.html)
- [S3 object ownership](https://docs.aws.amazon.com/AmazonS3/latest/dev/about-object-ownership.html#ensure-object-ownership)
- Direct connect gateway is a global resource
- __AWS Resource Access Manager__ to share TGW, subnets, license, route53 resolvers rules with many AWS account or within AWS Organization


  
