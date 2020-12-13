_braincert practice exam 1_

... be careful sometimes the question asks for 3 answers.

# Design for New Solution

- __Kinesis Video Streams__ can be used to stream, store Videos, and play back via HTTP Live Streaming (HLS). It also support batch analytical jobs.
- direct connect - [private virtual interface & public virtual interface](https://docs.aws.amazon.com/directconnect/latest/UserGuide/WorkingWithVirtualInterfaces.html), transit virtual interface
  - public VI for connecting to non-VPC service in AWS (can be in a different region)
  - private VI for connecting to VPC (must be in the same region as the Direct Connect connection)
  - transit VI for connecting to a transit gateway
  - hosted virtual interface for sharing the connection to another account
- direct connect gateway is a global resource
  - bind with virtual private gateway
    ![image](https://user-images.githubusercontent.com/60513695/102006691-fd8c1100-3d5d-11eb-8a71-143769f914c9.png)
  - bind with transit gateway
    ![image](https://user-images.githubusercontent.com/60513695/102006716-34622700-3d5e-11eb-8709-d23a1de52ebe.png)    
- S3 files produced by a tape gateway cannot be accessed by using S3 API. It requires Tape Gateway API. This hinders usages from Lambda or other analytics tools. Use a faile gateway instead. Sometimes the question might trick you by stating the on-prem system is storing data in a tape library
- use Route53 with weighted routing for canary rollout. this works with minimum effort even with cloudfront caching content (the cache will not conflict as the origin is different)




# Design for Organizational Complexity

- [Using latency and weighted records in Route 53 to route traffic](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/TutorialLBRMultipleEC2InRegion.html) 
  ![image](https://user-images.githubusercontent.com/60513695/102006182-1b577700-3d5a-11eb-9f83-945ea2737d4a.png)
- Cannot use static IP with ALB. [Consider a hybrid mode of putting ALB behind NLB, which can have static IP](https://aws.amazon.com/blogs/networking-and-content-delivery/using-static-ip-addresses-for-application-load-balancers/).
- [Route53 private hosted zone](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zone-private-considerations.html)
  - set _enableDnsHostnames_ and _enableDnsSupport_ of the VPC to true
  - Route53 health checks only failover, multivalue answer, and weighted records
  - if a custom DNS server is configured, the server must route private DNS queries to Amazon DNS server in the VPC, i.e. +2
  - multiple VPCs can be associated with a private hosted zone
- [centralize DNS management in multi-account environment](https://aws.amazon.com/blogs/security/how-to-centralize-dns-management-in-a-multi-account-environment/)
  - ![image](https://user-images.githubusercontent.com/60513695/102007473-4f379a00-3d64-11eb-9134-313bb6867c4e.png)
  - VPC Peering between participating account and central DNS-VPC
  - use DHCP option set of participating VPC to set the resolver to the IP of the DNS server in DNS-VPC
  - associate DNS-VPC with private hosted zones in each participating account. this needs to be done programmatically, or via CLI, because it is cross-account
  - DNS server in DNS-VPC uses conditional forwarder to send query either to Route53 resolver or to on-prem. resolver
  - limitations:
    - will break PrivateLink using private DNS, because the endpoint-DNS mapping is local to the account that owns the endpoint
    - EFS mount using DNS is not supported
- [improved centralize DNS management in multi-account environment](https://aws.amazon.com/blogs/security/simplify-dns-management-in-a-multiaccount-environment-with-route-53-resolver/)
  - ![image](https://user-images.githubusercontent.com/60513695/102007877-8eb3b580-3d67-11eb-8e0e-b9c4f1057114.png)
  - __no VPC-peering is needed__
  - use Route 53 Resolver endpoints: inbound endpoint + outbound endpoint for conditional forwarding
  - create forwarding rules for the resolver endpoints, and use __AWS Resource Access Manager (RAM)__ to share it with participating accounts or via AWS Organization
  - still need to associate private hosted zone with DNS-VPC programmatically or via CLI
- for resolution in private hosted zone from associated VPC in another account, UDP/TCP on port 53 is not needed (route 53 handles that automatically)
- AWS Organization 
  - from the root account create a sub-account, the root user and a role named _OrganizationAccountAccessRole_ with admin-permissions will be automatically created in the sub-account
  - from the root account invite an existing account, the role needs to be created manually
  - enable _trusted access_ to allow _trusted service_ to perform tasks in sub-accounts. it will create service-linked role in each account with predefined permissions
  - for SSO to work, need to enable AWS SSO as a trusted service
- promiscuous mode is not allowed in VPC. try IDS/IPS agent on web server instance or proxy server instance


# Continuous Improvement for Existing Solutions
- Route53 Private DNS records only work within the VPC. explicitly setting resolver on on-prem. vms to the local Route53 resolver won't work.
- To access Storage Gateway from a different place
  - launch a new Storage Gateway instance AMI in EC2 and restore from a snapshot
  - create a EBS volume from the gateway snapshot, and mount to an EC2 instance
  - launch a new Storage Gateway virtual iSCSI device and restore from a gateway snapshot
- CloudWatch Event is called __AWS EventBridge__ now. It is a serverless event bus service.
- use EventBridge and CloudTrail to employ more sophisticated routing and filtering of events between S3 and Lambda [ref](https://aws.amazon.com/blogs/compute/using-dynamic-amazon-s3-event-handling-with-amazon-eventbridge/)


# Cost Control

- use EMR on spot instances for unpredictable workload. Also the reserved instance is not cost-effective if the job only needs to be run for a few hours per day.

# Migration Planning

- __AWS Appstream 2.0__ for desktop and application streaming. watch out for "a desktop client and server infrastructure"
