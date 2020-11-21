# AWS Organization

## Features

- centralized management of all AWS accounts
  - create/invite account to join the organization
  - attach policies that affect some or all of the accounts
  
- consolidated billing for all member accounts
  - consolidated billing is a feature of AWS Organization
  - consolidate and pay for all member accounts
  - management account can access billing information, account information and account activity of the member accounts. 
  - use Cost Explorer to improve organization's cost performance

- hierachical grouping of accounts to meet budgetary, security, or compliance needs
  - group accounts into organizational units (OUs)
  - attach different policies to each OU
  - e.g. group accounts into one OU for certain regulartory requirements
  
- __service control policies__ (SCPs)
  - SCP include restrictions to AWS services, resources, and APIs
  
- __tag policies__
- AI services opt-out policies  
- __backup policies__
  
- integration with AWS services
  - IAM service-linked role for the service in each member account
  - this role has predefined IAM permissions; all accounts in organization automatically have a service-linked role
  
- AWS Organization is a __global service__ that works from any and all AWS regions

- data replication is eventually consistent

- free to use. charge incurred from AWS services in other accounts

## Service Control Policies

- SCP does not grant permission, but defines guardrail or limits
- identity-based or resource-based policies are still needed for granting permissions
- effective permissions are the logical intersection between SCP and IAM/resource-based policies
- SCP affect only IAM users and roles
- SCP doesn't affect resource-based policies
- SCP affects all users and roles in attached accounts, including the root user.
- SCP do not affect any service-linked role


## Trusted service

- use trusted access to enable a supported AWS service to perform tasks in the organization and its accounts
- trusted service creates an IAM role called a service-linked role in every account in the organization
- the role has predefined permissions policy that only allow specific tasks to be performed in the account

