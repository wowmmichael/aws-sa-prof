_braincert practice exam 5_

# Cost Control

- Glacier supports read by byte range

# Design for Organizational Complexity

- use Cloudformation StackSets to create, update, or delete stacks across multiple accounts and regions
  - require __enable all features__ in AWS Organizations. all features include SCPs and tag polices as well as advanced account management features
  - enable trusted access with AWS Organization to StackSets
- DHCP options cannot be modified. Create a new set to replace the existing one.
- AWS System manager State Manager is used to configure the EC2 in a defined state, such as boot with specific software at start-up, download and update agents on defined schedules, configure network settings.
- For Direct connect vs. VPN in routing, there is fixed priority:
  1. BGP propagated routes from an AWS Direct Connect connection
  2. Manually added static routes for a Site-to-Site VPN connection
  3. BGP propagated routes from a Site-to-Site VPN connection
  4. For matching prefixes where each Site-to-Site VPN connection uses BGP, the AS PATH is compared and the prefix with the shortest AS PATH is preferred.
  
# Continuous Improvement for Existing Solutions

- default ASG healthcheck checks EC2 status only. so it is usually useful to use ELB healthcheck in ASG healthcheck
- ElasticCache Memcached supports multi-AZ, but doesn't support replication. 
- use trusted singers to generated signed URL to restrict access to private content in Cloudfront
  
# Migratin Planning

- When migrating EC2 instance, PEM keys will not be copied to new region, but authroized_keys will be baked into AMI ase nd become available in the new region. It is advised to use `--exclude directory` when creating the AMI
- RDS VMware allows for one-click migration to RDS using replica promotion. But be sure to promote first, then create extra read replicas

# Desgin for new solution

- Sometimes the question uses the phrase "Security Group uses implicit deny all rule", which is considered correct...
- cannot use route-table to control traffic between different subnets inside a VPC, because the default rule allows subnets to be reachable from other subnets
- security group and nacl are not relevant for encryption-in-transit for EFS 
- ElasticSearch Domain can have either public access or VPC access, but it cannot be modified after the domain is created.
- To use _Requester Pays_ feature for accessing S3, the request cannot be made via cross-account role.
- SQS FIFO queue needs to be used together with Message Group ID to guarantee order
- for identity federation via a identity broker, both _GetFederationToken_ and _AssumeRole_ can be used.
