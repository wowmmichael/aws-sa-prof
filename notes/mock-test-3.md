_braincert practice exam 1_

... be careful sometimes the question asks for 3 answers.

# Design for New Solution

- __Kinesis Video Streams__ can be used to stream, store Videos, and play back via HTTP Live Streaming (HLS). It also support batch analytical jobs.
- direct connect - [private virtual interface & public virtual interface](https://docs.aws.amazon.com/directconnect/latest/UserGuide/WorkingWithVirtualInterfaces.html), transit virtual interface
  - public VI for connecting to non-VPC service in AWS
  - private VI for connecting to VPC (must be in the same region as the Direct Connect connection)
  - transit VI for connecting to a transit gateway
  - hosted virtual interface for sharing the connection to another account
- direct connect gateway is a global resource
  - bind with virtual private gateway
    ![image](https://user-images.githubusercontent.com/60513695/102006691-fd8c1100-3d5d-11eb-8a71-143769f914c9.png)
  - bind with transit gateway
    ![image](https://user-images.githubusercontent.com/60513695/102006716-34622700-3d5e-11eb-8709-d23a1de52ebe.png)




# Design for Organizational Complexity

- [Using latency and weighted records in Route 53 to route traffic](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/TutorialLBRMultipleEC2InRegion.html) 
  ![image](https://user-images.githubusercontent.com/60513695/102006182-1b577700-3d5a-11eb-9f83-945ea2737d4a.png)
- Cannot use static IP with ALB. [Consider a hybrid mode of putting ALB behind NLB, which can have static IP](https://aws.amazon.com/blogs/networking-and-content-delivery/using-static-ip-addresses-for-application-load-balancers/).
- 

