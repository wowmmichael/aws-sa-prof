# Deployment and Operations Management

## Types of deployments

- types
  - big bang
  - phased rollout
  - parallel adoption
- Rolling deployment
  1. create new launch configuration with the updated AMI
  2. then start terminating old EC2 instances
  3. when instance is restarted, it will use the new AMI
- A/B Testing
  1. EC2 running different versions of the application is put behind separate load-balancers
  2. Route 53 send different weight of traffic to different load-balancers
  3. changing the weight overtime
- Canary Release
  1. a few EC2 instances running the new version are put in the same auto-scaling group that runs the old version
  2. monitor how the new versions behave
- Blue-Green Deployment
  - similar to A/B testing
  - methods
    - update DNS with Route 53 to point to a new ELB or instance
    - swap auto-scaling group already primed with new version instances
    - change auto-scaling group launch configuration to use new AMI version and terminate old instances
    - swap environment URL of Elastic Beanstalk
    - clone stack with AWS OpsWorks and update DNS
    
- Blue-Green Contraindication
  - data store schema changes too tightly coupled to the code changes
  - application requies special upgrade routines
  - off-the-shelve product is not blue-green friendly
  
## CI, CD, CD
- terminology
  - continuous integration: merge code changes back to main as frequently as possible
  - continuous delivery: automated release process to deploy at the click of a button
  - continuous deployment: code changes passes all stages of release process is onto production without human intervention

- development lifecycle
  1. sync repo
  2. make change
  3. unit testing
  4. commit to repo
  5. integration testing
  6. acceptance testing
  7. deployment to production
  8. smoke testing
- considerations
  - smaller, incremental compartmentalized improvements and features
  - lower deployment risk and limit negative impact
  - test automation must be STRONG
  - feature toggle patterns
  - microservice architecture is well suited for CI/CD
  
- AWS Deployment Lifecycle Tools
  - AWS CodeCommit
  - AWS CodeBuild
  - AWS CodeDeploy
  - AWS CodePipeline
  - AWS X-Ray
  - AWS CodeStar
  
## Elastic Beanstalk

_popular in exam_
- orchestration service to ease deployment of scalable web landscapes
- wide range of supported platforms - from Docker to Php, to Java
- multiple environments (DEV, QA, PROD, and etc.)
- deployment options
  - all at once; downtime + manual rollback
  - rolling: no downtime + manual rollback
  - rolling with additional batch: no downtime + manual rollback
  - immutable: no downtime + terminate new instances for rollback
  - traffic splitting: no downtime + reroute DNS to rollback
  - blue/green: no downtime + __Swap URL__ to rollback
  
## Cloudformation

- infrastructure as code
- repeatable, automatic deployments and rollbacks
- nest common components for reusability
- supports over 300 resource types
- supports custom resources via SNS or Lambda
- components
  - resources
  - stack policies: protect resources from being unintentionally deleted and updated
    - apply stack policy via CLI
    - once applied the policy cannot be removed, but can be modified via CLI
    - default to DENY everything
- best pratices
  - python "help scripts" insall software on EC2 instances
  - use cloudformation to make changes
  - use Change Sets to identify potential trouble spots
  - use stack policies to explicitly protect sensitive portions
  - use version control systems to manage the cloudformation templates
  
## Elastic Container Service

- components
  - ECS
    - AWS-specific platform for Docker containers
    - leverages AWS services like Route53, ALB, and CloudWatch
    - "Task" is a collection of containers for a service, but each of the container is more or less isolated
  - EKS
    - easy to "Lift and Shift" from k8s
    - a hosted k8s platform, which handles many things internally
    - "Pod" is a collection of containers collocated with one another and have shared access to each other
    
- ECS launch types
  - EC2
    - explicitly provision EC2 instances
  - Fargate
    - the control plane asked for resources and Fargate automatically provisions
    - manage upgrading, patching compute 
    
## API Gateway

  

  
  
