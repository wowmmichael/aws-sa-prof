# [Network ACLs Basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-basics)

- VPC comes with a modifiable default NACL. Each VPC must be associated with a NACL.
- default NACL
  - allows all inbound traffic
  - allows all outbound traffic
- new custom NACL
  - denies all inbound traffic
  - denies all outbound traffic
- a NACL can be associated with multiple subnets, but a subnet can only be associated with one NACL at a time
- NACL contains a list of rules order by number from the lowest to the highest.
- NACL has separate inbound and outbound rules
- each rule can be either _allow_ or _deny_
- NACl is stateless
- quota exists for
  - number of NACLs per VPC
  - numer of rules per NACL
  
# NACL rules

- components
  - rule number - used to indicate priority
  - type - e.g. SSH
  - protocol - any protocol that has standard protocol number (same as the protocol in Security Group)
  - port range - destination port range for the traffic (for both outbound and inbound)
  - source - inbound only, the source CIDR of the traffic 
  - destination - outbound only, the destination CIDR of the traffic
  - allow/deny
- example inbound rule
  - 100, HTTP, TCP, 80, 0.0.0.0/0, ALLOW
    - Allows inbound HTTP traffic from any IPv4 address.
  - 140, CUSOM TCP, TCP, 32768-65535, 0.0.0.0/0, ALLOW
    - Allows inbound return IPv4 traffic from the internet (that is, for requests that originate in the subnet).
- example outbund rule
  - 100, HTTP, TCP, 80, 0.0.0.0/0, ALLOW
    - Allows outbound IPv4 HTTP traffic from the subnet to the internet
  - 120, CUSTOM TCP, TCP, 32768-65535, 0.0.0.0/0, ALLOW
    - Allows outbound IPv4 responses to clients on the internet (for example, serving webpages to people visiting the web servers in the subnet).
- for every rule that you add, ensure that there is a corresponding inbound or outbound rule that allows response traffic
- [NACL for load balancers in VPC](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-security-groups.html#elb-vpc-nacl)
  - public load balancers 
    - inbound
      | source | protocol | port |
      |--------|----------|------|
      | 0.0.0.0/0 | TCP | listener|
      | VPC CIDR | TCP | 1024-65535 |
    - outbound
      | destination | protocol | port |
      |--------|----------|------|
      | VPC CIDR | TCP | instance listener|
      | VPC CIDR | TCP | health check |
      | 0.0.0.0/0 | TCP | 1024-65535 |
  - private subnet for instances
    - inbound
      | source | protocol | port |
      |--------|----------|------|
      | VPC CIDR | TCP | instance listener|
      | VPC CIDR | TCP | health check |
    - outbound
      | destination | protocol | port |
      |-------------|----------|------|
      | VPC CIDR    | TCP      | 1024-65535 |
      
      
- require explicit allow rule with both inbound and outbound to support Path MTU Discovery
  - type: Custom ICMP Rule; port range: Distination Unreachable, fragmentation required, and DF flag set
  - SG doesn't need this because SG is stateful ([ref](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html#path_mtu_discovery))
  - [Jumbo frames 9001 MTU](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html#jumbo_frame_instances)
    - you can configure the MTU size by route, or use multiple elastic network interfaces with different MTU sizes and different routes.
  
  

  
