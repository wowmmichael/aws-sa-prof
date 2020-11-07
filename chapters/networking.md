# Concepts

- refreshers
    - Physical layout of AZ and region
    - VPC
    - private and public subnets
    - NAT [doc](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)
    - "Disable source/destination checks"
    - routing table (default routes, local routes)
    - ipv4, subnet mask notation
    - gateway vs. router
- route table
    - gateway route table [doc](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html#gateway-route-table)
        - target can be either 'local' or an elastic network interface 'eni-id'
    - local gateway route table [doc](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html)
    - VPN routing [doc](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html)
    - route propagation
- gateway
    - internet gateway
    - virtual private gateway
- AWS outposts [doc](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html)
    - subnet
        - Outposts subnet 
- Site-to-site VPN connection
    - Internet Protocol security (IPsec) VPN connections between VPC and on-premises network
    - Virtual private gateway is the VPN concentrator on the AWS side of the site-to-site VPN
    - Autonomous System Number (ASN) is a concept in Broader Gateway Protocol (BGP)
    - Route table can be broadcasted by the customer gateway device (via BGP) to the VPC route table, which is called route propagation
- AWS VPN Cloudhub
- VPC endpoints and VPC endpoint service (AWS PrivateLink) [doc](https://docs.aws.amazon.com/vpc/latest/userguide/endpoint-services-overview.html)
    - Endpoints are supported within the same Region only
    - An interface endpoint supports TCP traffic only.
    - For each interface endpoint, you can choose only one subnet per Availability Zone.
    - Service endpoint vs. gateway endpoint
- AWS Direct Connect [doc](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)
- VPC Peering [doc](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)
    - VPC peering is not transitive
- Security Group and Network ACL
    - comparision [doc](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison)
    - Security Group
        - apply to each ec2 instance
        - only allow rules, no deny rules
        - can define separate rules for inbound and outbound
        - rule uses IP, Port range, and protocol 
        - stateful!
        - default rule: no inbound, any outbound
        - if there are multiple network interfaces, security group is bound to individual NI
    - Network ACL
        - default rule: all inbound, all outbound
        - stateless

- ephemeral ports


# Notes

- Default VPC has a default subnet in each AZ.
- A subnet must be associated with a route-table. Optionally, a route-table can be associated with a gateway.
- Every route table comes with a local route for VPC (or many if VPC has more than one ipv4 CIDR block). Local route cannot be deleted or modified.
- 'longest prefix match' is used to determine the routing rule to apply.
- A route-table cannot be associated with a gateway if any of the following is true:
    - The route-table contains targets other than a network interface or the default local route.
    - The route-table contains routes to CIDR blocks outside the range of VPC.
    - Route propagation is enabled
- Direct connect vs. Site-to-site VPN
- Comparing Virtual Private Gateway, Transit Gateway, and Direct Connect Gateway [link](https://www.megaport.com/blog/aws-vgw-vs-dgw-vs-tgw/)
- Resolve conflict in the propagated route table
    - the local route is most preferred even if the propagated routes are more specific.
    - prioritize the static routes whose targets are an internet gateway, a virtual private gateway, a network interface, an instance ID, a VPC peering connection, a NAT gateway, a transit gateway, or a gateway VPC endpoint.
- Routing by virtual private gateway 
    1. Longest prefix match applies
    2. If the prefixes are the same:
        1. BGP propagated routes from an AWS Direct Connect connection
        2. Manually added static routes for a Site-to-Site VPN connection
        3. BGP propagated routes from a Site-to-Site VPN connection
        4. BGP routing by comparing 'AS PATH' and 'multi-exit discriminators (MEDs)'
- Redundant Site-to-Site VPN connection to provide failover [doc](https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-redundant-connection.html)
- NAT gateway fault-tolerance
    - Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone. 
    - To create an Availability Zone-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone.
- NAT gateway cannot be associated with a security group, but is ok with network ACL.
- NAT gateway cannot be used by the other side of the peering network through VPC peering, site-to-site VPN connection, and AWS direct connect.
- NAT instance has security group. The "source/destination" check needs to be disabled, as this default ec2 check stops NAT from functioning.
- multicast is not allowed in VPC