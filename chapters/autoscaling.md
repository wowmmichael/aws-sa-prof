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
