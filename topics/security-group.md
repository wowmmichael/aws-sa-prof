# [RDS access control via Security Group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html)

- by default, network access is disabled for a DB instance
- types
  - VPC security group
    ![image](https://user-images.githubusercontent.com/60513695/101279197-93beb500-37fb-11eb-9d44-b44b51ea4a7f.png)
    - controls access to DB instances and EC2 instances inside a VPC
    - source:
      - a range of CIDR
      - another VPC security group
        - allow access from all intances that use the source VPC security group
    - govern both inbound and outbound traffic, but outbound traffic typically do not apply to DB instances
    - port must be specified; also specify the protocol as TCP
    - [access RDS from on-prem clients/applications](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/inter-network-traffic-privacy.html)
      - enable connectivity via AWS Site-to-Site VPN or AWS Direct Connect
      - use AWS-published API operations
      - clients must support at least TLS 1.0, but 1.2 is recommended.
      - clinets must also support Perfrect Forward Secrecy (PFS), such as Ephemeral Diffie-Hellman or Elliptic Curve Diffie-Hellman Ephemeral
      - must sign requests using access key from IAM principal, or STS
  - DB security group
    - controls access to EC2-Classic DB instances not in a VPC but on the EC2-Classic platform
    - source:
      - a range of CIDR
      - an EC2-Classic security group
    - governs inbound traffic only
    - no need to specify port. port number defined for the DB instance is used. no need to specify protocol.
  - EC2 classic security group
    - controls access to EC2 instance
    - EC2-Classic platform is a shared network; it is no longer supported after 2013-12-04

