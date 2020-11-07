# AWS VPC


## AWS VPC Security

- In a VPC, both Security Groups and Network ACLs together help to build a layered network defence.
- __Security groups__ - Act as a virtual firewall for associated instances, controlling both inbound and outbound traffic at the instance level
- __NACL__ - Act as a firewall for associated subnets, controlling both inbound and outbound traffic at the subnet level

![image](https://user-images.githubusercontent.com/60513695/98441361-b3998500-2138-11eb-9fc7-aff6f0ce6fb1.png)

### Security Groups

- Acts at an Instance level and not at the subnet level.
- Each instance within a subnet can be assigned a different set of Security groups
- An instance can be assigned 5 security groups with each security group having 60 rules
- Allows separate rules for inbound and outbound traffic
- Can sepcify __Allow__ rules, but not __Deny__ rules
- Defaults Security Group:
  - Not allow external inbound traffic but allow inbound traffic from instances within the same security group
  - Allow all outbound traffic
- Newly created Security Group is the same as the default security group
- Target is IP / CIDR range / another security group in the VPC / a peer VPC (requires VPC peering connection)
- Evaluated as a whole with most permissive rule taking precedence
- __Stateful__ - responses to allowed inbound traffic are allowed to flow outbound regardless of outbound rules, and vice versa
  - Connection Tracking
    - Security groups are stateful as they use connection tracking to track information about traffic to and from the instance
    - Connection Tracking is maintained only if there is no explicit Outbound rule for an inbound request, and vice versa
    - However, if there is an explicit outbound rule for an inbound request, the response traffic is allowed on the basis of the outbound rule and not on the tracking information
- Associated with ENI (network iterfaces), and moves together with ENI if ENI is attached to another instance

### Network Access Control Lists

- NACL is an optional layer of security for the VPC that acts as a firewall for controlling traffic in and out of one or more __subnets__
- applicable to all instances in the subnet
- separate inbound / outbound rules, and each rule can either allow or deny traffic
- Default NACL:
  - Allows all inbound and outbound traffic
- Newly created NACL:
  - Denies all inbound and outbound traffic
- A subnet can be assigned only 1 NACL, or is associated implicitly with the default NACL if there is no explict assignment
- NACL can be asscoiated with multiple subnets
- NACL is a numbered list of rules that are evaluated in order from the lowest numbered rule.
  - Rules can be conflicting with one another, but the one with lower number wins. e.g. if you have a Rule No. 100 with Allow All and 110 with Deny All, then all the traffic will be allowed
- __Stateless__


## References

- [jayendrapatil](https://jayendrapatil.com/aws-vpc-security-group-vs-nacls/)


