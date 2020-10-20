[acg forum](https://acloud.guru/forums/aws-csa-pro-2019/discussion/-L_joCzK9FTbpP2P7H0Z/how_i_passed_the_new_aws_solut)

1. Lots of AWS Organizations scenario (Multi AWS accounts)

2. Difference between SCP vs IAM Policy

3. AWS Organizations which has multiple Organizational Units (OU)

4. All services within Systems Manager ( Automation, Run Command, Session Manager, State Manager, Patch Manager, Maintenance Window)

5. When to use AWS Serverless Application Model (SAM) vs CloudFormation in deploying Lambda with DynamoDB

6. Server Migration Service (SMS) + DMS + SCT - Can you migrate non-VM servers using SMS?

7. AWS Rekognition

8. AWS Mechanical Turk, AppStream?

9. AWS CI/CD Services (CodeCommit, CodeBuild, CodeDeploy, CodePipeline)

10. S3 Requester Pays

11. AWS Config and its integration with other services. Like managing "approved" AMI.

12. Managing S3 Bucket Permissions - Notify if there is a publicly accessible object in the bucket. Trusted Advisor vs AWS Config?

13. AWS WAF - applying rules for ELB, CloudFront, Amazon API Gateway and EC2

14. AWS Shield Advanced vs AWS Shield Standard

15. Amazon ES (Elasticsearch?) - Kibana

16. Providing access to data and visualization tool: QuickSight vs Kibana

17. Direct Connect, Direct Connect Gateway, VIFs and LAG

18. Lambda accessing a database from outside your VPC.

19. Transit VPC + Connecting hundreds of VPCs in your on-premise data center

20. A difficult scenario on VPC Peering when one VPC is peered with 2 VPCs which uses Longest Prefix Match.

21. Migration on-premise IBM MQ / WebSphere? Use Amazon MQ or EC2?

22. Private Hosted Zone in Route 53 to connect the routing of your multiple VPCs..

23. Lambda@edge when authenticating a website

24. Improving CloudFront performance (Cache Hit Rate?)

25. X-Ray vs Inspector vs Systems Manager

26. Athena, S3 Select, Glacier Select differences

27. Provisioned IOPS vs GP2,

28. DynamoDB Streams

29. AWS Batch,

30. AWS Directory Service

31. SWF vs Step Functions

32. Cached Volume vs Stored Volume vs File vs Tape Gateway

33. VPC Endpoint + Private vs Public VIF?

34. 6 Rs of migration: https://aws.amazon.com/blogs/enterprise-strategy/6-strategies-for-migrating-applications-to-the-cloud/





Training material:

acloudguru -Not enough to crack the exam

linux academy: Boring

udemy : Stephane Maarek (Good)

Zeal Vora( good, too much content)

Practice exams: Whizlabs( poor worded, unnecessary complexity in questions)

jonbonso( recommended)

acloudguru and linux academy(ok , ok)

I realised None of the training material in the world replaces aws whitepapers, aws blogs and faq. This helped me pass the exam in 2nd attempt

As I remember these are the topics I have seen questions around

Oracle RDS migration ; migration sequence

Oracle Dataware house migration , scenarios based on time bound

Stepfunctions /SWF -with lambda SQS

postgresql to unstructured json format DB

S3 upload scenarios..(access denied errors, unable to upload)

sensitive Data masking when writing data to s3

Application discovery scenarios(agent/agentless)

Multi account scenario access (direct connect gateway, transit gateway, privlink)

private virtual interface and public virtual interface situations

S3 with cloudformation restrict access to content

s3 with cloudformation improve user response, reduce latency

s3 going serverless scenarios

reliability with SQS, lambda and event bridge

cloudformation scenarios with codecommit, codepipeline

cloudformation scenarios with chef scripts

Coudformation scenarios with systems manager

Disaster recovery 5 minutes recovery method

AWS budgeting , cost explore, tags