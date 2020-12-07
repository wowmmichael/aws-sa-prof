# [Security group basics](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#VPCSecurityGroups)

- virtual firewall for instance to control inbound and outbound traffic
- assign up to five security groups to the instance
- security group act at the instance level, not the subnet level; each instance in a subnet can be assigned to a different set of SGs.
- instance automatically assigned to the default security group of the VPC by default
- can specify _allow rules_, but not _deny rules_
- separate rules for inbound and outbound traffic
- filter traffic based on protocols and port numbers
- stateful, for both inbound and outbound
- new security group
  - no inbound rule
  - one outbound rule that allows all outbound traffic
- default security group
  - allow instances within the SG to communicate with each other
  - inbound:
    - (source) sg-xxxx (protocol) All (port range) All
  - outbound:
    - (destination) 0.0.0.0/0 (protocol) All (port range) All
    - (destination) ::/0 (protocol) All (port range) All
  - can be modified      
- quotas apply
  - number of SGs per VPC
  - number of rules per SG
  - number of SGs to associate with a network interface
- instances associated with a SG cannot talk to each other without explicit rules being added; the default SG has them by default
- SGs are associated with network interface. 
  - by default, SGs associated with the instance are associated with the primary network interface (eth0)
  - like instance, when creating a network interface, it is automatically associated with the default security group
- SG name cannot start with _sg-_ because that indicates a default SG
- SG can only be used inside the VPC declared at the creation time of the SG

# [Security Group Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules)

- inbound rule component
  - source of the traffic, either another security group, an IPv4 or IPv6 CIDR block, a single IPv4 or IPv6 address, or a prefix list ID
  - destination port or port range
  - protocol that has a standard protocol number 
- outbound rule component
  - desntination of the traffic, similar to the source for the inbound rules
  - destination port or port range
  - protocol like above
- using SG as the source for a rule means
  - traffic is allowed from the network interfaces that are associated with the source SG for the specified protocol and port
  - incoming traffic is allowed based on the private IP address associated with the source SG (not the public IP or Elastic IP addresses) 
- SG only supports filtering on destination ports (for both inbound and outbound), unlike other firewalls
- updating the rules will automatically apply the changes to all instances associated

# [AWS Firewall Manager](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#aws-firewall-manager)

- manages SGs across multiple accounts and resources centrally in AWS Organizations
- apply common SGs to specified accounts and resources
- audit SG rules to locate and remediate noncompliant rules
- audit usage of Sgs to clean up unused and redundant SGs


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


# [Reference peer VPC groups in security groups](https://docs.amazonaws.cn/en_us/vpc/latest/peering/vpc-peering-security-groups.html)

- update inbound or outbound rules for VPC security groups to reference security groups in the peered VPC
- allows traffic to flow to and from instances associated with the referenced security group
- VPC can belong to the same or different accounts
- refeenced security group must reside in the __same region__
- for peer VPC in a different region, use the CIDR block of the peer VPC
- VPC peering connection must be _active_
- when a security group rule becomes stale, it's automatically removed.

