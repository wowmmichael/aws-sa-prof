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

## [Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/introduction.html)


### Network-to-VPC connectivity options

#### AWS managed VPN

- AWS-managed VPN endpoint to connect to customer's gateway using IPSec over the internet
![image](https://user-images.githubusercontent.com/60513695/98445916-637ceb80-2155-11eb-818a-427e47bc2cb6.png)
- support both static and dynamic routing options
- dynamic routing uses BGP peering to exchange routing information
  - can specify routing priorities, policies, and weights in BGP advertisements and influence the network path between your networks and AWS
  - require the user gateway device to be capable of terminating both IPSec and BGP connections
- VGW is not the initiator; CGW must initiate the tunnels
- use keepalive pings to prevent the tunnel from going down due to idle connection
- limitations:
  - latency and availability depend on internet conditions
  - customer managed endpoint is responsible for implementing redundancy and failover
  - customer device must support single-hop BGP
  
#### AWS Transit Gateway + VPN

- use AWS Transit Gateway to connect multiple VPCs in the same region to the customer gateway using IPSec over the internet
![image](https://user-images.githubusercontent.com/60513695/98446082-3f6dda00-2156-11eb-80fe-089515430b7b.png)

#### AWS Direct Connect

- use Direct Connect, which is dedicated network connection over private lines, to avoid public internet
- require additional telecom and hosting provider relationships or new network circuits to be provisioned
![image](https://user-images.githubusercontent.com/60513695/98446188-c28f3000-2156-11eb-9293-0e829658b852.png)
- uses 802.1q VLANS to connect to AWS VPC using private IP addresses
- 1 Gbps or 10 Gbps dedicated network connections
- different gateways on the AWS's side
  - Virtual Private Gateway
  - Direct Connect Gateway is a __globally__ available resource to enable connections to multiple VPCs accross regions and accounts
  - AWS Transit Gateway is a network transit hub to interconnect VPCs in the same region
- VPN over Direct Connect for end-to-end security

#### AWS VPN CloudHub

- operates simple hub-and-spoke model, commonly used to to setup connectivity among remote offices
- uses an AWS VPC virtual private gateway with multiple customer gateways, each using unique BGP autonomous system number (ASN). 
- sites must not have overlapping IP ranges
![image](https://user-images.githubusercontent.com/60513695/98446848-99bd6980-215b-11eb-9851-9428d8afa837.png)

#### Software Site-to-Site VPN

- allows for the flexibility to fully manage both sides of VPC connectivity, for compliance reasons or leveraging gateway devices not yet supported by AWS's VPN solution
![image](https://user-images.githubusercontent.com/60513695/98447400-7eecf400-215f-11eb-80b9-7e80b06e1c75.png)

#### AWS Client VPN

- AWS managed high availability and scalability service that enables secure software remote access
- supports creating a secure TLS connection between remote clients and AWS VPC
- OpenVPN clients only

![image](https://user-images.githubusercontent.com/60513695/98447452-d5f2c900-215f-11eb-9b34-3990e3814b1f.png)


### VPC-to-VPC connectivity options

#### VPC peering

- connect __two__ VPCs that enables routing using each VPC's private IP addresses as if they are in the same network
- supports __cross-account__ and __inter-region__ peering
- traffic always stays on the global AWS backbone and never traverse the public internet (sounds a bit like VPC endpoints)
- AWS leverages the existing infrastructure of a VPC to create VPC peering connection, so they do not introduce additional SPOF or bandwidth bottleneck
- VPC peering is __not transitive__
- in a VPC peering connection, the VPC __does not__ have access to any other connection the peer VPC may have, including:
  - VPN connection or AWS Direct Connect to on premise network
  - internet connection through an internet gateway
  - internet connection through a NAT device
  - VPC endpoint to AWS services, e.g. an endpoint to S3
- only one peering connection can be established between the same two VPCs
- tags are only applied in the account or region in which the peering was created
- instance's public DNS can be resolved to its private IP address across peered VPCs


#### AWS Transit Gateway

- is a highly available and scalable service with a hub-and-spoke architecture; provides simpler VPC-to-VPC communication management than VPC peering
- each spoke VPC only needs to connect to the Transite Gateway to gain access to other connected VPCs
- Transit Gateway across different regions can peer with each other to enable cross-region connections
- traffic always stays on the global AWS backbone
![image](https://user-images.githubusercontent.com/60513695/98447167-99be6900-215d-11eb-99b6-15537ce79e16.png)

#### Software Site-to-Stie VPN

- very similar to the one in 'Network-to-VPC connectivity options'
- uses an internet gateway attached to each VPC to facilitate communication between the software VPN appliances

#### AWS Managed VPN

- very similar to the one in 'Network-to-VPC connectivity options'
- suboptimal from a routing perspective since the traffic must traverse to router on customer's network
- gives more flexibility for controlling routing and potentially reuses VPN connections
![image](https://user-images.githubusercontent.com/60513695/98447302-abecd700-215e-11eb-9f0c-3dd560c4ab58.png)


#### AWS PrivateLink

- see 'PrivateLink Endpoint'
![image](https://user-images.githubusercontent.com/60513695/98447329-f2423600-215e-11eb-9f0b-3716bf36200b.png)



## References

- [jayendrapatil](https://jayendrapatil.com/aws-vpc-security-group-vs-nacls/)
- [vpc-sg-nacl comparison](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison)

## Related

- [Stroage Gateway Networking FAQ](https://aws.amazon.com/storagegateway/faqs/)

