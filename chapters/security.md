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
  
