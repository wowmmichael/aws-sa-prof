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
- Target is IP / CIDR range / another security group in the VPC / a peer VPC (requires VPC peering connection)
- Evaluated as a whole with most permissive rule taking precedence
- __Stateful__ - responses to allowed inbound traffic are allowed to flow outbound regardless of outbound rules, and vice versa
  - Connection Tracking
    - Security groups are stateful as they use connection tracking to track information about traffic to and from the instance
- Associated with ENI (network iterfaces), and moves together with ENI if ENI is attached to another instance


