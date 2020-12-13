_braincert practice exam 6_

# Migration Planning

- migrate on-prem database:
  - if the network/big is too small, use Snowball to transfer the data
  - capacity limit: Aurora MySQL 64TiB, Aurora PostgreSQL 32TiB, RDS MySQL 16TiB
  - migration with [SCT and Snowball Edge](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html)
- watch out for _reduce capacity management and infrastructure maintenance_ , which suggests re-platform (e.g. cassandra to dynamodb)
- VPC interface endpoint DNS names
  - When you create a VPC endpoint service, AWS generates endpoint-specific DNS hostnames that you can use to communicate with the service.
    - private DNS name support needs to be enabled to make request to the service using the default DNS name
    - use interface VPC endpoint to access EC2 in provider VPC from consumer VPC
  - [AWS PrivateLink and VPC peeriing](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peer-region-example.html)

- EMR master, core, task nodes. Use spot instances for task nodes.
- ELB/NLB with ECS
  - no need to register target with target group because ECS register/deregister containers automatically
  - for Fargate, _awsvpc_ network mode is required, so must choose _ip_ as the target type instead of _instance_
- cannot access S3 via direct connect using service endpoint, but it is possible to do that by setting up public virtual interface
- AWS SCT to convert relational DB objects from commercial engine, including Oracle, SQL Server, IBM Db2 on LUW (linux, unix, windows), to open-source engine; DMS to move data and keep database in sync


# Continuous Improvement for Existing Solutions

- don't be tricked by the processing time of a batch job to eliminate options of using a Lambda function. if the run pattern is changed, e.g. from daily to per-event-trigger, the process time will be greatly reduced. 
- __AWS Support Plan__ needs to be at least at Business Level to support API access, such as invoking from Lamda. Sometimes, the question will only explicitly state requirement on rate limit check, but implies API access implicitly
- to restart intance with the same IP, ASG cannot be used. instead, use __CloudWatch alarm action__ to recover instance
- [ECS/Lambda blue/green deployments through CodeDeploy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html)
- AWS Serverless Application Model, a.k.a. SAM
- HOST header is used for virtual hosting, such as SNI. It is not needed for HTTPS, session, and authorization. So it can be removed from the Cloudfront header whitelist to increase the cache hit ratio.
- use cloudformation with cross-stack reference doesn't automate the entire flow. it only makes passing parameters easier.
  - consider Step function
  - consider products grouped in portfolio in AWS Service Catalog
- Cloudfront can integrate with Cognito via Lambda@edge [ref](https://aws.amazon.com/blogs/networking-and-content-delivery/authorizationedge-using-cookies-protect-your-amazon-cloudfront-content-from-being-downloaded-by-unauthenticated-users/); API Gateay has built-in support for Cognito integration.
- using cloudfront to access signed-url for S3 doesn't make sense, because it breaks caching. use S3 Transfer Acceleration instead.
- improve Lambda performance by reusing containers to avoid unnecessary startup time, and increasing memory. cannot increase cpu because it is adjusted automatically based on memory.


# Design for New Solution

- [transit VPC with third-party routing appliance](https://aws.amazon.com/marketplace/solutions/infrastructure-software/transit-VPC)
- call center suite
  - __Amazon Connect__ : cloud-based contact center
  - __Amazon Lex__ : chatbot, speech recognition, natural language understanding (powers __Amazon Alexa__)
- __Route 53__ health check
  - HTTP/HTTPS health check requires TCP connection in 4s, response in 2s after connecting
  - HTTPS check does not validate SSL/TLS certificate
  - HTTP stauts must be 2xx or 3xx
  - Optional string matching, Route 53 only searches the first 5120 bytes of the body for the string. Also the response body must be received within 2s 
- export AWS CloudWatch Logs to S3 does not support SSE-KMS enabled bucket. but AES-256 is ok.
- KMS keys are regional. Same keys cannot be shared across regions
- Certificates are also regional resource. Use AWS Certificate Manager to request certificate for each region.
- CloudWatch Logs can feed to S3 or Kinesis Data Firehose
- CloudWatch Log Events can generate CloudWatch metrics using filters
- update lauch configuration setting in ASG does not affect existing instances. but in cloudformation, replacing existing instances can be achieved by setting _UpdatePolicy__ attribute to _AWS::AutoScaling::AutoScalingGroup

  
  
# Cost Control

- On-demand instance, Reserved instance, Spot instance
- AWS really wants you to upgrade AWS Support Plan to Business and above. One benefit is Trusted Advisor can provide better suggestions on optimization and cost saving
- Scheduled Reserved Instances vs. Reserved Instance


# Design for Organization Complexity

- watch out for AWS Batch with EFS, which may be hard to manage. using EBS is more common. also EFS is more expensive than EBS
- Amazon Mechanical Turk
- To create an organization, master account send invitation to secondary account instead of the reversed join request.
- Watch out that for SCP, a full access policy to a particular service has to be provided. This is also true for the deny mode because AWS provides a default allow-all policy.
- in cloudformation, use stack policy to prevent unintentional update to stack, and use DeletionPolicy to specify how to manage resource after the stack is deleted
- WAF doesn't integrate with NLB because NLB is level 4, but ALB is ok. Use Shield to protect Route 53, Cloudfront, and public subnet.



