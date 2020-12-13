_braincert practice exam 2_

# Design for New Solution

- using AWS PrivateLink to establish secure connection between microservices without traversing the public internet
- AWS OpsWork uses [attributes](https://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-installingcustom-components-attributes.html) to pass sensitive values, but it does not encrypt attributes
- AWS OpsWork supports setting up database layer in addition to web server layer
- cloudfront video delivery
  - on demand (VOD) - store the content in original format in S3; AWS Elemental Media Convert to transcode the video into streaming format, and store the transcoded in S3; deliver converted content by using a Cloudfront distribution
  - livestreaming - use either Elemental MediaPackage or Elemental MediaStore as origin



# Continuous Improvement of Existing Solution

- DynamoDB can work with Apache Hive and EMR using EMR-DynamoDB connector
- AWS Data Pipeline is used to define data-driven workflows for moving and transforming data
- restrict AMI for launching EC2 instance via IAM [ref](https://aws.amazon.com/premiumsupport/knowledge-center/restrict-launch-tagged-ami/)
- detect and alert non-approved AMI via AWS Config
- __AWS Inspector__ is a service that deploys agent into EC2 instance to check for exposure, vulnerabilities, and generates finding or report
- EBS Snapshot Lifecycle Policy can be configured on daily, weekly, monthly, or yearly schedule, but not hourly
- stop and start an EC2 instance in "impaired" status so the instance may be able to find a healthy host. reboot doesn't do that
- frontend interacting with DyanmoDB is not considered as a bad practice... multiple questions accepted that as answer


# Cost Control
- EBS gp2 can increase IOPS linearly against volume with roughly 3 IOPS per GiB (for gp3 IOPS can be increased via request) capped below 16,000 IOPS. Within this range, gp2 is much more cost effective than provisioned IOPS SSD such as io2 
- provisioned IOPS SSD can provide maximum 64k IOPS. for requirement that is even higher, use EC2 instance store
- Snowball volume is 50T, 80T with less usablable space

# Migration Planning

- DMS can use ElasticSearch, Neptune as target. DMS supports SAP ASE, IBM DB2, MongoDB as source in addition to traditional relational databases.


