# AWS VPC

![image](https://user-images.githubusercontent.com/60513695/98444451-ba31f780-214c-11eb-9f5e-debda2cdf8b0.png)

## Components

#### Internet Gateways - IGW
- horizontally scaled, redundant, and highly available VPC component that allows instances in VPC to communicate with the Internet
- no availability risks or bandwidth constraints
- purposes:
  - provide a target in the VPC route table to represent Internet-routable traffic
  - perform NAT for instances that don't have public IP addresses
- steps to enable Internet access to an instance
  - attaching IGW to VPC; the corresponding subnet is called public subnet
  - subnet should have route tables with the route pointing to IGW
  - instances should have public IP or Elastic IP assigned
  - security groups and NACL applicable to the instance should allow the relevent traffic
  
#### NAT

- NAT instance is launched in the public subnet, and needs to be assigned an Elastic IP or a public IP
- NAT instance should __disable Source/Destination check flag__
- perform both address translation and port translation
- no redundancy and bandwidth may be insufficient
  - more sophiscated setup to gain redundancy via auto-scaling group
- useful scenarios:
  - instances in private subnets need internet connection for software updates
- or use NAT Gateway, which is a managed service by AWS
  - higher bandwidth, can support bursts of up to 10 Gbps
  - for higher requirement, the workload can be distributed into multiple subnets and creating a NAT gateway in each subnet
  - NAT Gateway is associated with one Elastic IP and cannot be disassociated
  - created in a specific AZ with redundancy inside AZ
  - supports TCP, UDP, and ICMP
  - cannot be associated with a security group. NACL can be used to control the traffic
  

#### Egress-only IGW

- works like a NAT gateway, but for IPv6 traffic __only__
- higly available like IGW
- allows egress traffic, and prevents the Internet from initiating connection with the instances
- use a NAT gateway for IPv4 traffic

#### VPC endpoints

- enable private connection between VPC to supported AWS services and VPC endpoint services powered by PrivateLink
- does not require public IP address; the access can be over the internet, NAT device, a VPN connection, or AWS Direct Connect
- Traffic between VPC and AWS service does not leave the Amazon network
- Endpoints are virtual devices, that are horizontally scaled, redundant, and highly available VPC components
- no availability risks or bandwidth constraints
- Currently does not support cross-region requests
- VPC Endpoint Policy is an IAM resource policy attached to an endpoint controlling access from the endpoint to the specified service. Defaut allows full access. Endpoint policy does not override or replace IAM user policies or service-specific policies (such as S3 bucket policy)
- two types of endpoints:
  - VPC Gateway Endpoints
    - A VPC Gateway Endpoint is a gateway that is a target for a specified route in the route table, used for traffic destined to a supported AWS service. Currently supports S3 and DynamoDB
    ![image](https://user-images.githubusercontent.com/60513695/98444736-498bda80-214e-11eb-9756-02c524783de0.png)
    - in the VPC, service is accessed via the endpoint
    - endpoint needs to be associated with the route table, e.g. a rule with destination pl-68a54001 (com.amazonaws.us-west-2.s3) and a target with the endpoint ID (vpce-12345678) 
    - security groups need to allow outbound traffic from the VPC to the service. Used the __service prefix list ID__ e.g. com.amazonaws.us-east-1.s3  as the destination in the outbound rule
    - multiple endpoints can be created for a single service, but they cannot be specified in a single route table. commonly used to enforce different access policies from different subnets
  - [VPC Interface Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html)
    - enables connectivity to services powered by AWS PrivateLink, including some AWS services (e.g. CloudTrail, CloudWatch), services hosted by other customers and partners in their VPCs, and supported AWS Marketplace Partner services
    - support private DNS by setting `enableDnsHostnames` to true
    ![image](https://user-images.githubusercontent.com/60513695/98445526-29124f00-2153-11eb-98f1-1b71a4191183.png)
    - interface endpoint can be used to connect to on-premises data center, through AWS Direct Connect or AWS Site-to-Site VPN
    
    


## Route Tables

- Route table defines routes, which determine where network traffic from the subnet would be routed
- Each VPC has an implicit __router__ to route network traffic
- Each VPC has a Main Route Table, and optionally multiple custom route tables
- Each subnet must be associated with a __single__ route table at a time, defaults to the main route table
- Every route table contains a local route which cannot be modified or deleted, e.g.
  | Destination | Target |
  |-------------|--------|
  | `10.0.0/16`   | Local  |
  | `2001:db8:1234:1a00::/56` | Local |
- Route priority is decided by matching the most specific route in the route table
- Rules for IGW, VPG, VPC Peering, VPC Endpoints, NAT Device
  ![image](https://user-images.githubusercontent.com/60513695/98443014-93bb8e80-2143-11eb-8cbf-985893b8bc5c.png)
- Route propagation require subnet route table to enable route propagation, then when attaching a virtual private gateway, routes representing Site-to-Site VPN connection automatically appears as propagated routes
  - Static routes always take priority over the propagated routes
- Gateway route tables
  - Route table can be associated with an internet gateway or a virtual private gateway, to use fine-grain control over the path for traffic __entering__ the VPC
  - Supports routes where the target is `local` or an ENI in the VPS that is attached to middlebox applicances
    - Destination is entire CIDR block of the VPC, which is equivalent to local
    - Destination is the CIDR of a subnet
  - Foridden:
    - Route table contains routes with targets other than ENI or the default local
    - Route table contains CIDR blocks outside the range of VPC
    - Route propagation is enabled for the route table
    - Require ENI to be attached to a running instance


  
  





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
- Target is IP / CIDR range / port range / another security group in the VPC / a peer VPC (requires VPC peering connection)
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
- A rule specifies __Type__ (e.g. SSH, All IPv4 traffic, HTTPS), __Protocol__ (e.g. TCP), Port range, __Source__ CIDR (for inbound rule), and __Destination__ CIDR (for outbound rule)
- Ephermeral ports 1024-65535 is often opened to allow outbound traffic to different clients
- If MTU between subnets is different, must add network ACL rules to allow type-Custom ICMP Rule with and port range-Destination Unreachable, fragmentation required, and DF flag set  


## References

- [jayendrapatil](https://jayendrapatil.com/aws-vpc-security-group-vs-nacls/)
- [vpc-sg-nacl comparison](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison)

