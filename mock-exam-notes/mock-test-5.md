_braincert practice exam 3_

# Design for New Solution

- TCP load balancing with __CloudHSM__ deployed in multiple AZ to perform SSL transaction
- __CloudHSM__ key access is controlled by a command line tool. IAM cannot help with the individual key access control.
- ticky phrases: "store in S3 bucket using server-side encryption" and "encryption-at-rest using S3-managed encryption keys" are not the same thing
- Storage Gateway stored/cached volume does not provide file level restore; consider storage gateway file store instead.
- Consider adding SQS into the stack to buffer very spiky requests (words like in a short period of time the site will receive millions of requests) even the backend is DynamoDB. 
- SNS doesn't send email. SES does.
- __Rekognition__ detects and labels objects, celebrities, and inappropriate content in images and videos
  - images must be JPEG or PNG, and can be S3 object or a byte array
  - videos must be MP4 or MOV in H.264 codec
- __AWS Lex__ integrates with __AWS Comprehend__ for sentiment analysis
- IAM user is global. no need to reassign the policy in each region
- use built-in functions of CloudFormation to assign dynamic values such as AZ for ELB, and AMI ID for launch configurations
- set cache TTL to 0 in cloudfront will let cf to request origin with an _If-Modified-Since_ header

# Continuous Improvements for the Existing Solution

- Separating business units to different accounts is correct but would induce greater disruption. On the other hand, implementing tagging policy and creating IAM policies to limit permission based on tagging is preferred.
- use __IAM certificate store__ to manage SSL/TLS certificate in regions that don't support __ACM__
- IPSec VPN is for connecting two LANs; SSL VPN is for connecting a client to a LAN
- EFS performance depends on the storage size with bursting throughput mode


# Design for Organization Complexities

- provide users with additional IAM permissions to allow them to use each of the underlying resources in a product's cloudformation template so that user can launch a product from __AWS Service Catalog__, or use [Launch Constraints](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/constraints-launch.html)
  - launch constraint is per product-portfolio association
  - by using Launch Constraint, the end user dosn't need permissions for resources like Cloudformation and other resources used by the product
  - notification constraint specifies SNS to receive notification about stack events
  - tag update constraint allows/disallows end users to update tags on resources provisioned by product
- Service Catalog Template Constraints are used to limit options that are available to end users when launching products
  - template constraint is bound to portfolio
- EBS DLM use tag to back up EBS volume independently if there are multiple tags
- use __AWS Config__ to periodically audit changes to AWS resources and monitor the compliance of the configurations
- use CloudWatch to monitor system events pattern for compliance. CloudTrail does not by itself provide ability to detect and notify problems
- CloudFormation can be used to model OpsWorks components. The resource type is _AWS::OpsWorks::Stack_


  
# Migration Planning

- Route53 uses alias record to pont to ELB
- use __Snowmobile__ for migrating 100PB of data

# Cost Control

- S3 supports reading a specific range of bytes in an object by using the _Range_ HTTP header

