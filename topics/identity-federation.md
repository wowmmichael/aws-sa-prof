# [Identity providers and federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)

- problem to address
  - grant external user identity permissions without having to create user in AWS account
  - authentication via organization's authentication system or a well-known identity provider such as Google, Amazon
- delegation to IDP
  - create an IAM identity provider entity to establish a trust relationship between AWS account and the IDP
  - IAM supports IDP that is compatible with OpenID Connect (OIDC) or Security Assertion Markup Language (SAML 2.0)  
- web identity federation
  - flow
    1. Configure mobile/web application in IDP to obtain a unique ID for the application (audience)
    2. create an IDP entity for OIDC compatible IDP in IAM
    3. create IAM role that has
      - trust policy - specify the IDP as the principal (the trusted entity) and include a Conidtion that matches the App ID assigned by the IDP
      - permission policy - specify the permissions the application can assume
    4. application calls the sign-in interface for the IDP to login
    5. IDP authenticates the user and returns an authentication token (OAuth access token or OIDC ID token)
    6. application calls STS with the _AssumeRoleWithWebIdentity_ action to request temporary security credentials with:
      - IDP's authentication token
      - ARN for the IAM role created for the IDP
    7. AWS verifies the token and the trust relation. STS responds with temporary security credentials that can be used to assume the role. The response also includes metadata about the user obtained from the IDP
    8. application can access AWS resources with the role, and use the user ID to customize operations for the user
       ![image](https://user-images.githubusercontent.com/60513695/101480260-6b1af480-398e-11eb-8c1b-3688a298b958.png)     
- identity federation with Cognito
  - Cognito is recommended for all web identity federation scenarios
  - Cognito supports both authenticated and unauthenticated (anonymous) access
  - Cognito helps synchronization user data across devices and providers
    ![image](https://user-images.githubusercontent.com/60513695/101481033-7c183580-398f-11eb-9759-4df9dc84bb56.png)
    
- (historical) [token vending machine](https://aws.amazon.com/articles/authenticating-users-of-aws-mobile-applications-with-a-token-vending-machine/)
  - TVM uses STS to provide temporary credentials
  - identity TVM & anonymous TVM

    
- SAML 2.0 based federation
  - SAML 2.0 based federation enables federated single sign-on (SSO), so users can log into AWS Console or invoke AWS APIs without an IAM user
  - useful in organizations that have integrated identity systems such as Windows AD or OpenLDAP
  - flow
    1. create a SAML provider entity in AWS using the SAML metadata document provided by IDP to establish trust between AWS account and the IDP
    2. SAML metadata document contains
      - issuer name
      - creation date
      - expiration date
      - keys for validating authentication assertions
    3. create IAM role that has
      - trust policy - specify the SAML provider as the principal
      - permission policy - specify the permissions the user can assume
    4. SAML trust is completed by configuring the IDP with information AWS and the roles that the federated users can use; (referred as configuring relying party trust between IDP and AWS)
    5. application calls the sign-in interface for the organization IDP to login
    6. IDP authenticates the user and generates a SAML authentication response, which includes assertions that identify the user and attributes about the user
    7. application calls STS with _AssumeRoleWithSAML_ action to request temporary security credentials with
      - ARN of the SAML provider entity
      - ARN of the role to assume
      - SAML assertion about the current user
      - the time for the credential to be valid
      - (optional) IAM policy to further restrict the permissions to the user
    8. AWS verifies the SAML assertion and the trust relation. STS responds with temporary security credentials for assuming the role named in the request. The response also contains metadata about the user.
    9. application can access AWS resources with the role, and use the user ID to customize operations for the user
      ![image](https://user-images.githubusercontent.com/60513695/101486978-6eb37900-3998-11eb-80ea-5962744391c9.png)
  - flow for SSO
    - _AssumeRoleWithSAML_
    ![image](https://user-images.githubusercontent.com/60513695/101487056-8db20b00-3998-11eb-9bdc-19e9cebdbefb.png)
- custom identity broker federation
  - flow
    - _GetFederationToken_ or _AssumeRole_
    ![image](https://user-images.githubusercontent.com/60513695/101487315-f4cfbf80-3998-11eb-9845-cb572459ba49.png)

    

    
