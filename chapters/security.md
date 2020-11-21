# Security

- Shared responsibility concepts
- Principle of least privilege
- Security Facets
  - Identity : Root Account User, IAM User, Temporary Security Credentials
  - Authentication : Multi-factor authentication, Client-side SSL
  - Authorization : IAM Policies
  - Trust : Cross-Account Access, SAML-based Federation, Web Identity Fedaration
- Typical components
  - Service Provider
  - Identity providers
  - Identity store
  - Identity Broker
  - Identities
- SAML vs. OAuth vs. OpenID
  - SAML 2.0
    - handle both authorization and authentication
    - XML based
    - assertions in XML for authentication, attributes for authorization
    - best suited for SSO for enterprise users
  - OAuth 2.0
    - authorization only
    - share protected assets without send login credentials
    - token based, delegate access
    - best suited for API authorization between apps
  - OpenID Connect
    - layer on top of OAuth 2.0 to include authentication
    - REST/JSON message flows
    - extensible
    - best suited for SSO for consumer applications
- Compliance
  - AWS Artifacts
  
# Multi-account management

- motivations
  - isolation between workloads
  - limited visibility and discoverability of workloads
  - minimize blast radius
  - strong isolatin of recovery and auditing data
- tools
  - AWS Organization
    - Service control policies
  - Tagging
  - Resource Groups
  - Consolidated Billing
- structures
  - identity account structure
    - all user accounts in identity account
    - rely on trust relationship from IAM roles to gain access to operations account
  - logging account structure
    - centralized logging repository in the logging account
    - data is secured and immutable from the operations account
    - use Service Control Policies (SCP) to prevent sub-accounts from changing the logging settings
  - publishing account structure
    - common repositories for AMI's, containers, code
    - permits sub-account to use pre-approved services or assets
  - information security account structure
  - central IT account structure

# Network controls and security groups

- security group
  - virtual firewalls for individual assets (EC2, RDS, AWS Workspaces, etc.)
  - control inbound and outbound traffic for TCP, UDP, ICMP, or custom protocols
  - use port ranges
- NACL
- multi-layer of Least-Privileges practice


# Directory Services
- service options
  - AWS Cloud Directory
    - control access to hierachical data with complex relationships between applications
  - Amazon Cognito
    - scalable sign-up and sign-in functionalities
  - AWS Directory Service for Microsoft Active Directory
    - AWS managed full MS AD running on Windows Server 2012 R2
  - AD Connector
  - Simple AD
- AD Connector vs. Simple AD
  - AD Connector
    - must have existing AD
    - AD user can access AWS assets via IAM roles
    - supports MFA
  - Simple AD
    - stand-alone AD based on Samba
    - no trust
    - no MFA
    - kebero based authentication
    
# Credential and Access Management
- concepts
  - IAM and components
  - users, groups, roles, and policies
  - resource-based policies vs. identity-based policies
  - services -> actions -> resources in IAM policies
- STS vs. Cognito
- Fedaration token to STS token to access AWS resources
- Token vending machine concept
  - common way to issue temporary credentials for mobile app development
  - anonymous TVM - provide access to AWS services only, does not store identity
  - identity TVM - used for registration and login, and authorization
  - recommended to use Cognito than TVM
- AWS secrets manager
  - store passwords, keys, SSH keys, PGP keys
  - fine-grained access control provided by IAM
  
# Encryption
- encryption at rest & encryption in transit: SSL/TLS for HTTPS, IPSec for VPN
- Key management service (KMS)
  - tightly integrated into AWS services
  - audit use of keys via CloudTrail
  - differs from secret manager as its purpose-build for encryption key management
  - compliances schemes (PCI DSS Level 1, FIPS 140-2 Level 2)
- CloudHSM
  - dedicated hardware device, single tenanted
  - must be within a VPC and can be access via VPC peering
  - does not integrate natively with as many AWS services as KMS. requires custom application scripting
  - offload SSL from web servers, issuing CA, enable TDE for Oracle databases
  - classic version vs. current version
    - current version: no up-front cost, clustered
- CloudHSM vs. KMS
  |    | CloudHSM | KMS |
  |----|----------|-----|
  |tenancy| single | multi |
  | availability | customer-managed durability and availability | availability and durability provided by AWS |
  | root of trust | customer-managed | AWS-managed |
  | FIPS 140-2 | Level 3 | Level 2 mostly |
  | 3rd party support | Broad 3rd Party Support | Broad AWS Service Support |
- AWS certificate manager
  - managed service for provisioning, managing, and deploying public or private SSL/TLS certificates
  - directly integrated into CloudFront, ELB, and API Gateway
  - free public certificates to use; needless to register via a 3rd party authority
  - supports wildcard domains
  - managed certificate renewal
  - support creating managed private certificate authority for internal and proprietary apps, services, or devices
  
  
# Distributed Denial-of-service attack
- Amplification/Reflection attacks
- best practices to be resilient against DDoS attacks
  - minimize attack surface: NACLs, SGs, VPC design
  - scale to absorb attacks: autoscaling groups, AWS cloudfront, static web content via S3
  - safeguard exposed resources: Route 53 (geo-based restriction), AWS WAF (web application firewall), AWS Shield
  - detect abnormaly: AWS GuardDuty, CloudWatch, CloudTrail
    - GuardDuty and CloudTrail focus on the security of the AWS account
  ![image](https://user-images.githubusercontent.com/60513695/99274448-4d61e000-2865-11eb-85be-0782d8e630c1.png)
  
# Intrusion prevention and detection
- Intrusion Detection System & Intrusion Prevention System
  - IPS is able to take automatic actions such as backlisting offending IPs
- log collection mechanism - security information and event management system (SIEM)
- CloudWatch vs. CloudTrail
  - CloudWatch
    - log events across AWS services
    - high-level monitoring and events
    - log from multiple accounts. commonly use dedicated account for CloudTrail
    - log stored indefinitely
    - alarm history for 14 days
  - CloudTrail
    - log API activities across AWS services
    - low-level and granular
    - log from multiple accounts. commonly use dedicated account for CloudTrail
    - log stored to S3 or CloudWatch indefinitely
    - can collect log from multiple regions into a single S3 bucket for a single account
    - no native alarming, can use CloudWatch alarms
    
# AWS Service Catalog
- features
  - frameworks for creating pre-defined products and landscapes
  - control user access
  - use adopted IAM roles so users don't need underlying service access
  - allow end users to be self-sufficient while upholding enterprise standards for deployments
  - based on cloudformation
  - admin can version and remove products without affecting running product versions
- constraints
  - launch constraint
  - notification constraint
  - template constraint
- multi-account scenarios
  - sub-accounts inherit the shared portfolio from master account
  - sub-accounts need to create IAM roles and policies by themselves
  - launch rules are inherited, which by default point to the resources in the master account. so it should be override by the sub-accounts.
  

# Pro Tips
- Acknowledge concerns and be ready with a process (Cloud Adoption Framework)
- Leverage assessments and checklists
- Migrate to the cloud is often more secure due to increased transparency and visibility
- Speak in terms of risk as a continuum rather than an absolute
- Consider AWS Certified Security, or CISSP


# References

- [AWS Security Best Practices](https://d1.awsstatic.com/whitepapers/aws-security-best-practices.pdf)
- [AWS Multi-account Security Strategies](https://d1.awsstatic.com/aws-answers/AWS_Multi_Account_Security_Strategy.pdf)
- [AWS Best Practices for DDoS Resiliency](https://d1.awsstatic.com/whitepapers/Security/DDoS_White_Paper.pdf)
- [Security Pillar](https://d1.awsstatic.com/whitepapers/architecture/AWS-Security-Pillar.pdf?ref=wellarchitected)

# Tips
- KMS does not store username and password; it is dedicated to encryption key management
  ![image](https://user-images.githubusercontent.com/60513695/99879129-aeaef800-2c45-11eb-8572-d12d03165598.png)
-
  
