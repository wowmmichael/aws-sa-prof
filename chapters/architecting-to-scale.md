

# Auto-scaling

- types
 - Amazon EC2 auto scaling
  - setup scaling groups for EC2 instances; health checks to remove unhealthy instances
 - Application auto scaling
  - API used to control scaling for other resources like Dynamo, ECS, EMR
 - AWS auto scaling
  - centrailized way to manage scalability for whole stacks; predictive scaling feature; console to provide holistic view
- EC2 auto-scaling groups
 - horizontal scaling (scale-out) EC2 instances
 - triggered by event or scaling action
 - availability, cost, and system metrics can all factor into scaling
 - scaling options
  - maintain - keep a specific or minimum number of instances running
  - manual - use maximum, minimum, or specific number of instances
  - schedule - increase/decrease instances based on schedule
  - dynamic - scale based on real-time metrics of the systems
 - auto-scaling group in launch configuration
  - specify VPC and subnets for scaled instances
  - define health-check grace period
  - attach to an ELB
 - dynamic auto-scaling policies
  - types
   - target tracking policy
   - simple scaling policy
   - step scaling policy
  - cooldown period
   - the amount of time for the new instances to come up to speed and absorb traffic
   - while health-check grace period is the time for the instance to start up
   - automatically applied to dynamic scaling, optional to manual scaling, but not supported for scheduled scaling
   - default to 300 seconds. can be overriden via scaling-specific cooldown, e.g. using lower cooldown period for scaling-down
  - scaling based on external metrics 
    ![image](https://user-images.githubusercontent.com/60513695/101572979-27b69980-3a13-11eb-899c-1574deaa6d60.png)
- application auto-scaling
 - types
  - target tracking policy
  - step scaling policy
  - scheduled scaling policy
- AWS Auto Scaling
 ![image](https://user-images.githubusercontent.com/60513695/101574875-cba04500-3a13-11eb-8751-3ca89ccd7347.png)
 - manage all the resources in a holistic view
 - scaling strategies phrased with business terms, e.g. "Optimize for availability" and "Balance availability and cost", or detailed "Custom" strategy
 - predictive scaling

   
   

# EC2 autoscaling

## Dynamic Scaling [link](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html)




# Application autoscaling



# Conecepts

- EC2 Simple scaling policy
- EC2 Step scaling policy
- Target tracking scaling policy
- [Instance warm-up](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-simple-step.html#as-step-scaling-warmup)
- [Application autoscaling step scaling policy](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-step-scaling-policies.html)
- [Cooldown period](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-step-scaling-policies.html#step-scaling-cooldown)
- Cloudfront behaviors
- KCL (Kinesis client library), KPL (Kinesis producer library)
 



# Notes

1. Step-based autoscaling in EC2 autoscaling and application autoscaling is different. The former is for EC2 and doesn't have cooldown period; the later is for other types of AWS resources.


2. If need autoscaling per minute, need to enable detailed monitoring in Cloudwatch, because the default only has per 5 minutes.

3. Use `stress` to create artificial load in instance to demo autoscaling.

4. SQS supports ingesting objects up to 2GB using the JAVA SDK.

5. Data stored in EMR cluster is ephermeral. Use S3 with EMRFS file system if persistence is needed.

6. Atomic functional units in a loosely coupled system is good for scalability.
