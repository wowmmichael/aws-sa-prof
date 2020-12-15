
# Cloudfront

- HTTPS between viewer and cloudfront
  - change _Viewer Protocol Policy_ to _Redirect HTTP to HTTPS_ or _HTTPS Only_
  - if not using alternate domain name, the cloudfront SSL/TLS certificate can be used instead of a custom certificate
    - one of the solution if some of the viewers don't support SNI
  

- HTTPS between cloudfront and custom origin using default CF domain name
  - update _Origin Protocol Policy_ (HTTPS Only, Match Viewer)
  - requires certificate signed by trusted third-party CA. if the origin is ELB, certificate by AWS Certificate Manager (ACM) can also be used
  - self-signed certificate __cannot__ be used
  - if the certificate is invalid or expired, Cloudfront will drop TCP connection and return 502 Bad Gateway error
  - the certificate must cover the domain specified for __Origin Domain Name__ in the distribution
  - in addition, if cloudfront is configured to forward the _Host_ header, the certificate must match the domain in the _Host_ header
  
  
- HTTPS when using alternate domain name 
  - options for cloudfront to serve HTTPS requests
    - use Server Name Indication (SNI) - recommended
    - use a dedicated IP address in each edge location
  - using SNI
    - SNI is an extension to the TLS protocol
    - cloudfront associates ADN with an IP for each edge location
    - DNS routes the viewer request to the IP for the correct edge location
    - the IP to the domain is determined during SSL/TLS handshake, it isn't dedicated to the distribution
    - viewer supporting SNI does:
      1. viewer get the domain name from the request URL and adds it to a field in the request header
      2. cloudfront finds the domain name in the request header and responds with applicable certificate
      3. viewer and cloudfront perform SSL/TLS negotiation
  - requirements for HTTPS between cloudfront and custom origin is the same as using default cloudfront domain name
  

- HTTPS between cloudfront and S3 origin
  - if S3 bucket is configured as a website endpoint
    - HTTPS between cloudfront and S3 is not supported
  - otherwise
    - HTTPS between cloudfront and S3 is always __Match Viewer__
    - change the value of _Viewer Protocol Policy_ to _Redirect HTTP to HTTPS_ or _HTTPS Only_
