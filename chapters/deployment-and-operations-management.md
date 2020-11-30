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

- managed, high available service to front-end REST APIs
- backed with custom code via Lambda, as a proxy for another AWS Service, or any other HTTP API on AWS or elsewhere
- regional based, private or edge optimized
- support API keys and usage plans
- using CloudFront behind the scenes so custom domains and SNI are supported
- can be published as products and mometized on AWS Marketplace

## Management Tools

- AWS Config
  - allows user to assess, audit, and evaluate configurations of the AWS resources
  - useful for configuration management as part of an ITIL program
  - track configuration changes
  - validation and violation detection
  - e.g. is backup enabled for RDS? is CloudTrail enabled? are EBS encrypted?
  
- AWS OpsWorks
  - managed instanc of Chef and Puppet
  - offerings
    - OpsWorks for Chef Automate
    - OpsWorks for Puppet Enterprise
    - OpsWorks Stacks
      - compatible with Chef recipes
      - supports EC2 instances and on-prem servers with an agent
      - stacks can only be cloned within the __same region__
  - OpsWorks is a global service, but stack can only control resources in the region where stack is created
  
- AWS System Manager
_very likely to appear in exames_
  - centralized console and toolset for system management tasks
  - designed for managing a large fleet of systems
  - SSM agent enables System Manager features and support many OSs
  - SSM agent is installed by default on AWS provided base AMIs for Linux and Windows
  - can manage on-prem systems
  - services
    - inventory
      - collect OS, application and instance metadata
    - state manager
      - create states that represent a certain configuration is applied to instances
    - logging
      - CloudWatch Log agent and stream logs to CloudWatch from instances
    - parameter store
      - shared secure storage of config data
    - insight dashboard
      - account-level view
    - resource groups
      - group resource via tagging for organization
    - maintenance windows
      - define schedules for patch, update apps, run scripts, and more
    - automation
      - automating routine maintenance tasks and scripts
    - run command
      - run commands and scripts without SSH or RDP
    - patch manager
  - system manager documents (SSM documents)
    - command document
    - policy document
    - automation document
    
# Enterprise Apps

- Amazon Workspace and AppStream
  - Workspace: fully managed desktop-as-service
  - AppStream: application hosting
  - use cases: remote or seasonal workers such as distributed call center.
  - use cases: demo product using AppStream
  
- Amazon Connect and Amazon Chime
  - Connect: managed cloud-basec contact center
  - Chime: online meeting and video conferencing
  
- Amazon WorkDocs and Amazon WorkMail
  - WorkDocs: online document storage and collaboration
  - WorkMail: like Exchange
  
- Amazon WorkLink and Alexa for Business
  - WorkLink: like VPN but simpler, usually mobile devices
  - Alexa functionality and skills internally in the enterprise
  
# Machine Learning Landscape

- ML frameworks and infrastructures
- ML services - Amazon SageMaker
- AI services - targeting developers without AI knowledge
  - Amazon Comprehend
  - Amazon Forecast
  - Amazon Lex
  - Amazon Personalize
  - Amazon Polly (text-to-speech service)
  - Amazon Rekognition
  - Amazon Textract (OCR)
  - Amazon Transcribe (speech-to-text service)
  - Amazon Translate
  

# References

- [Infrastructure as Code](https://d1.awsstatic.com/whitepapers/DevOps/infrastructure-as-code.pdf)
- [Practicing CI CD on AWS](https://d1.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
- [Overview of Deployment Options on AWS](https://d1.awsstatic.com/whitepapers/overview-of-deployment-options-on-aws.pdf)

  

  
  
