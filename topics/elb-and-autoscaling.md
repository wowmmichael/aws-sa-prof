- _[alb, nlb, glb, clb](https://docs.aws.amazon.com/elasticloadbalancing/index.html)_
- _[jayendrapatil aws_auto_scaling](https://jayendrapatil.com/aws-auto-scaling/)_

# Auto scaling & Elastic load balancer

- auto scaling dynamically adjusts the number of EC2 instances, while ELB manages distributing incoming requests to every running instance
- ![image](https://user-images.githubusercontent.com/60513695/101601374-960d5300-3a37-11eb-9936-d7b6e0ec9b75.png)
- attaching/detaching ELB with auto scaling group (ASG)
  - one or more load balancers can be attached to an existing ASG
  - after ELB is attached to the ASG, ELB automatically registers all the instances in the ASG
  - ELB registers EC2 instance using the IP address of the primary interface _etho0_ of the instance, and routes traffic to this IP
  - Auto Scaling adds instances to the ELB when new instance is launched
  - when ELB is detached, it will deregister instances in the ASG
  - if connection draining is enabled, ELB waits for in-flight request to complete before deregistering the instances
  - instances remain running after they are deregistered from the ELB
- high availability and redundancy
  - Auto scaling can span across multiple AZs within the same region
  - when one Az becomes unhealthy or unavailable, AS launches new instances in an unaffected AZ
  - ELB can be setup to distribute incoming requests across EC2 instances in a single AZ or multiple AZs within a region
  - traffic is load balanced equally across all AZs enabled for ELB
- health checks
  - ASG determines the health state of each instance by periodically checking the EC2 instance status check; AS replaces the instance if the instance fails the status check
  - ELB also performs health checks on the EC2 instances that are registered with it
  - AS does not replace instance if ELB health check fails; ELB does not route traffic to the instance 
  - AS can be configured to use the results of both EC2 instance status check and ELB health check to determine the health of the instance in ASG
- monitoring
  - ELB sends data about the load balancers and EC2 instances to CloudWatch
  - ASG can be configured to use ELB metrics to scale the application
  
# Auto scaling overview
- fault tolerance, availability, cost optimization
- spcecify scaling policies which is used to launch and terminate EC2 instances
- AS attempts to distribute instances evenly between AZs by attempting to launch new instances in the AZ with the fewest instances
- ![image](https://user-images.githubusercontent.com/60513695/101606053-e2f42800-3a3d-11eb-9f35-3eaeb94230e5.png)
- launch configuration
  - it is an instance configuration template that ASG uses to launch EC2 instance
  - can be associated with multiple ASGs
  - __launch configuration cannot be modified after creation__
  - by default, basic monitoring is enabled if the launch configuration is created with the management console; detailed monitoring is enabled when it is created with AWS CLI or API
- launch template
  - similar to launch configuration but with additional features and is recommended by AWS
  - allows __multiple versions of a template__ to be defined
  - supports both Spot and On-Demand instance types
  - supports EC2 Dedicated Hosts
- auto scaling group (ASG)
  - components  
    - _launch configuration_ or _launch template_
    - minimum and maximum capacity, desired capacity (defaults to minimum capacity)
    - AZs or subnets
    - metrics & health checks
  - ASG can launch On-Demand Instances, Spot Instances, or both, if configured to use a launch template
  - ASG can span multiple AZs, but not multiple regions
  - to merge separate single-zone ASG into a single multi-zone ASG, rezone one of the single-zone ASG into a multi-zone ASG, and then delete the other groups
  - ASG can only be associated with a single launch configuration
  - when launch configuration for the ASG is replaced, only new instances will use the new configuration; the existing instances are not affected
  - to delete a ASG, need to set the minimum and desired capacity to 0 first

- auto scaling options
  - maintain steady count of instances
    - AS ensures the desired count of instances will always be running
    - instance termination
      - AS replaces the instance if
        - instance is in a state other than _running_
        - the system status is _impaired_
        - (if AS uses ELB health check) ELB reports the instance as _OutOfService_
      - replacement happends immediately
      - when instance is terminated, Elastic IP as well as attached EBS volumes are detached automatically; they need to be attached to new instance manually
  - manual scaling
    - peformed by
      - changing the desired capacity limit of the ASG
      - attaching/detaching instances to the ASG
    - attaching instance has constraints about the instance such as VPC, AZ, and the instance state
    - attaching instance automatically increases the desired capacity of the ASG
    - detaching instance can optionally decrease the desired capacity of the ASG; otherwise, AS will launch new instances to replace the ones that are detached
    - detached instance is automatically unregistered from the ELB
    
  - scheduled scaling
    - defines a new set of minimum, maximum, and desired capacities and a time in future
    - multiple scheduled actions can be specified but the time must be non-overlapping
    - cooldown period is not supported
  - dyanmic scaling
    - uses a combination of alarm and policy
      - an alarm is an object that watches over a single metric over a specified period
      - a policy is a set of instructions that tell AS how to respond to an alarm
    - scaling process
      1. CloudWatch monitors the metrics in the ASG
      2. change in metrics breaches the threshold of CW alarm, then CW alarm performs an action
      3. a message is sent to either the scale-in policy or the scale-out policy
      4. AG performs the scaling activity 
    - scaling policy types
      - target tracking scaling - change the capacity by value proportional to metric difference
      - step scaling - change the capacity based on the size of the alarm breach
      - simple scaling - change the capacity based on a single scalign adjustment
    - multiple policies
      - each ASG has at least two policies: one for scale out and the other for scale in
      - when multiple policies are triggered, AS will choose the policy with the greatest impact
    - cooldown period
      - defaults to 300 seconds
      - interval between multiple scaling activities for __simple scaling__
      - during a cooldown period, when a scheduled action starts at the scheduled time, or when scaling activities due to target tracking or step scaling policies start, they can trigger a scaling activity immediately without waiting for the cooldown period to expire.
      - if an instance becomes unhealthy, Amazon EC2 Auto Scaling also does not wait for the cooldown period to complete before replacing the unhealthy instance.
      - not to confuse with [cooldown period in application auto scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-step-scaling-policies.html#step-scaling-cooldown)
- termination policy
  - decides which instances to terminate first during a scaling-in activity
  - default termination policy is used
    - select from the AZ with the most instances
    - select unprotected instances that use the oldest launch configuration, or
    - select unprotected instances that are closest to the next billing hour, or
    - select unprotected instances randomly
  - default termination policy can be replaced by a custom termination policy
    - AZ is still selected based on AZ imbalance
    - specify criteria using the following options
      - OldestInstnace
      - NewestInstance
      - OldestLaunchConfiguration
      - OldestLaunchTemplate
      - ClosestToNextInstanceHour
      - Default
  - in __ReplaceUnhealthy__ process, instance is terminated first, and new instance is booted subsequently
  - in __AZRebalance__ process, AS launches new instances in healthy AZ first before terminating instances in unhealthy AZ
    - notice some naunces caused by process suspension, e.g. Launch, Terminate
  
- standby state
  - used for troubleshooting
  - instance in the standby state will not receive requests; it is automatically deregistered from the ELB, and will be registered when it exits the state
  - AS by default decrements the desired capacity for the group so no new instance will be launched
  
  
# SSL termination
[ref](https://acloud.guru/forums/aws-well-architected-framework/discussion/-M--C1hiEC5dBDBhE0x9/Application%20load%20balancer%20-%20Connection%20between%20ALB%20&%20EC2)
The ALB will terminate HTTPS connections. When you create target groups ( which is what you use to organize the traffic going from the ALB to your ec2 instances) you can choose either HTTP or HTTPS protocol. Use HTTP to reduce the load on your ec2 instances and use HTTPS if you want the data to be encripted in transit all the way to your instances for compliance reasons. Note that the traffic is not encrypted fully end to end as the ALB itself will decrypt and then encrypt the data of each packet.

Use a NLB if for some reason you need complete end to end encryption or if you need really high throuput that the NLB supports. Use an ALB if you want more flexibility to support different forwarding rules based on the url or path of the requests or you want to terminate the TLS connection at the LB.
  

  
    
    
  
  
  

  
