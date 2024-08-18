## CloudFront Architecture

![Global Content Delivery and Optimization-08-03-2024](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024.png)

![Global Content Delivery and Optimization-08-03-2024-1](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-1.png)

* CloudFront is a Content Delivery Network capable of caching static and dynamic content.
* Improves the delivery of content from its original location to the viewer of that data through Caching.
* You could have one or more origins.

Origin
	\- original or source location of the content.
	\- S3 or a Custom Origin (e.g. Web Server with Public IPv4) .

Distribution
	\- unit of configuration within CloudFront.
	\- always has at least 1 Behavior and can have more.
	\- you can configure an AWS WAF, Alternate Domain Names, Custom SSL Certificates, and more.

Behaviors
	\- a configuration within a Distribution.
	\- configured with a path pattern.
	\- Origins, Origin Groups, TTL, Protocol Policies, restricted access can be configured here.

Edge Locations
	\- pieces of Global Infrastructure where your content is Cached.
	\- over 400 as of 2024.

Regional Edge Cache
	\- bigger than an Edge Location.
	\- designed to \hold more data to Cache things which are less frequently accessed.

* Domain names always end with cloudfront.net

Cache Hit
	\- If the object has been cached to an Edge Location it would be returned immediately.

Cache Miss
	\- object is not yet stored at an Edge Location.
	\- then it will be checked on the Regional Edge Cache if its not there there will be an origin fetch (retrieval of object from origin).

* It integrates with AWS Certificate Manager (ACM) for SSL certificate usage.
* CloudFront is for download style operations only, uploads go directly to the origin.
* Origins are linked to Behaviors which are linked to Distributions.

## CloudFront - TTL and Invalidations

![Global Content Delivery and Optimization-08-03-2024-2](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-2.png)

304 Code
	\- object is not modified.

200 OK Code
	\- object is modified from source and will be retrieved.

* An Edge Location views an object as not expired when it’s still within its Time-To-Live (TTL) Period.
* More frequent Cache Hits means lower origin load.
* Objects cached by CloudFront have a Default TTL (Behavior) Period or Validity Period of 24 Hours.
* You can also set the Minimum TTL and Maximum TTL which are the lower and upper TTL values an individual object can have.
* You can define per object values for the TTL.

**Different Headers in CloudFront**

* Minimum and Maximum TTL are limiters for the Headers below.
* Headers can be set using Custom Origin (Injected by Application/Server) or S3 (via Object Metadata).

1. Cache-Control max-age (Seconds)
2. Cache-Control s-maxage (Seconds)
3. Expires (Date & Time)

**Cache Invalidations**

* Cache Invalidations are performed in a Distribution.
* Applied to all Edge Locations within that Distribution when set.
* What it does is that it immediately expire any objects regardless of their TTL based on the Invalidation Pattern specified.
* Cost is the same regardless the number of objects that is pattern matched. 
* If you are regularly updating/invalidating individual files use Versioned File Names instead.

## AWS Certificate Manager (ACM)

* HTTP was simple and not secure.
* HTTPS is HTTP with an added SSL/TLS Layer of Encryption.
* Data is encrypted in transit.
* Allows servers to prove their identity through SSL Certificates.

Chain of Trust
	\-  signed by a trusted authority.

* ACM can function both as a Public Certificate Authority, or a Private Certificate Authority.
* You generate or import Certificates.
* Use DNS or Email for verification.
* ACM generated Certificates are automatically renewed.
* ACM imported Certificates require manual renewal or certificates.
* Can only deploy Certificates to supported services.

 
Public Certificate Authority
	\- generating certificates which are trusted by public web browsers and devices.
	\- browsers trusts a list of providers.

Private Certificate Authority
	\- generation of certificates for applications within a Corporation or Enterprise group.
	\- you need to configure clients/applications to trust the Private Certificate Authority.

* ACM is a Regional service.
* Certificates cannot leave the Region they are generated or imported in.
* To use a Certificate with an ALB in a certain region you need a Certification in ACM in that region.
* Global services that require Certificates should have their Certificates generated/imported in US-East-1 (NV).

**ACM Architecture**

![Global Content Delivery and Optimization-08-03-2024-3](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-3.png)

## CloudFront and SSL/TLS

* Each CloudFront Distribution receives a Default Domain Name (CNAME) (htttps://<\random>.cloudfront.net/) upon creation.
* You can enable HTTPS access to your Distribution by Default as long as the Default DNS is used.

Alternate Domain Name (CNAMES)
	\- allows you to give an alternate DNS name instead of just the Default (*.cloudfront.net)

* Verify Ownership (Optionally HTTPS) of the Domain using a matching SSL Certificate.
* Generate or Import an ACM of your Alternative Domain in US-East-1 (NV).
* HTTP or HTTPS, HTTPS only, and HTTP => HTTPS conversion

**2 Sets of Connections when using CloudFront**

* Both connections require valid Public Certificates as well as any intermediate Certificates in the chain.
* Self-signed Certificates will not work with CloudFront.

1. Viewer Protocol
	\- connection between CloudFront and the Viewer.

2. Origin Protocol
	\- connection between CloudFront and the origin.

Server Name Indication (SNI)
	\- adds the ability for a client to tell a server which Domain Name its attempting to access.
	\- occurs at the TLS handshake.

* CloudFront charges extra for a Dedicated IP.

**SSL in CloudFront Architecture**

![Global Content Delivery and Optimization-08-03-2024-4](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-4.png)

* S3 Origins handle Certificates natively.
* At the origin side, the Certificate installed on any of your origins needs to match the DNS name that CloudFront is using to contact the origin.

**CloudFront - Origin Types and Origin Architecture**

Origins
	\- where CloudFront goes to get content if an Edge Location receives a request.

Origin Groups
	\- allows you to add resiliency.

**Categories of Origins**

1. Amazon S3
	\- viewer and origin protocol are matched.

Exclusive Feature/s Available
* Origin Access
	\- the ability to restrict access to an S3 Origin.
	\- makes it accessible only via CloudFront Distribution.
* Can pass through Origin Custom Headers

2. AWS Media Packet Channel Endpoints
3. AWS Media Store Container Endpoints
4. Custom Origins
	\- can select 3 different protocols (HTTP, HTTPS or Match the Viewer Protocol)
	\- can choose different ports.
	\- can use custom header for security.

**Securing CloudFront and S3 using Origin Access Identity (OAI) or Origin Access Control (OAC)**

* OAI is only applicable to S3 Origin.
* Common pattern is to lock an S3 down to being only accessible via CloudFront.

Origin Access Identity (Origin Access Control)
	\- a type of identity.
	\- can be associated with CloudFront Distributions.
	\- when a CloudFront Distribution is accessing an S3 origin it becomes an OAI.
	\- can be used within S3 Bucket Policies.

**OAI/OAC Architecture**

![Global Content Delivery and Optimization-08-03-2024-5](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-5.png)

**Securing Custom Origins Architecture**

![Global Content Delivery and Optimization-08-03-2024-6](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-6.png)

**2 Ways of Implementing a Secure Custom Origin**

1. Use a Custom Header
	\- the way this works is users use HTTPS to communicate with the Edge Locations via Viewer Protocol Policy.
	\- a Custom Header is sent along with the request the Origin Protocol Policy/Connection.
	\- origin will be configured to require the Custom Header otherwise it won’t service requests.
	\- Custom Headers are injected at the Edge Location.

2. Traditional Security Methods
	\- use the Publicized AWS IP Address range of the CloudFront Edge Locations.
	\- using a Traditional Firewall on the Origin.
	\- Firewall is then configured to allow connections from the Edge Locations and deny anything else.

## CloudFront - Private Distribution and Behaviors

* Upon creating a distribution you will only have 1 Behavior which dictates whether a Distribution is Public or Private.
* You can also have multiple Behaviors with each being either Public or Private.

**2 Security Modes when it comes to Content**

1. Public (Default)
	\- any content distributed via CloudFront is Public.
	\- can be accessed by any viewer.

2. Private
	\- any requests made to CloudFront need to be made with a Signed Cookie or Signed URL \or they’’ll be denied.

Old Method
	\* A root account user creates a CloudFront key.
	\* Once a CloudFront Key exists in that AWS account that account can be a Trusted Signer to a Distribution (specifically Behavior/s).

CloudFront Key
	\- this is tied to the whole AWS account.

New Method
	\* Create Trusted Key Groups and assign those as Signers.
	\* Key Groups determine which Keys can be used to create Signed URLs and Signed Cookies.

Signed URLs
	\- provide access to one object only.
	\- use this if clients don’t support Cookies.

Signed Cookies
	\- provides access to groups of objects.
	\- use for groups of files or all files of a type.
	\- for maintaining application URLs (no modification with URL required).

**Private Distribution Architecture** 
![Global Content Delivery and Optimization-08-03-2024-7](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-7.png)

## Lambda@Edge

* A feature of CloudFront which allows you to run lightweight Lambda Functions at CloudFront Edge Locations.
* Can adjust traffic between the viewer and the origin.
* Does not have the full Lambda feature set.
* Currently only supports Node.js and Python.
* Runs in AWS Public Zone (no access to VPC based resources).
* Lambda Layers are not supported.

**Lambda@Edge Architecture**

![[Pasted image 20240818083043.png]]

* Lambda@Edge can run on every part of the interaction to influence the traffic of the connection.

**Interaction Consists of 4 Parts**

1. Viewer Request
	\- connection from the viewer to the Edge Location.

2. Origin Request
	\- connection from the Edge Location to the origin.

3. Origin Response
	\- connection from the origin to the Edge Location.

4. Viewer Response
	\- connection from the Edge Location to the viewer.

**Lambda@Edge Limits (Viewer Side)**

* 128MB Memory Allocation
* Function Timeout of 5 seconds

**Lambda@Edge Limits (Origin Side)**

* Normal Lambda Memory Limit
* 30 second Timeout

**Use Cases**

* A/B Testing - Viewer Request Function
* Migration between S3 Origins - Origin Request Function
* Different Objects based on Device - Origin Request Function
* Content by Country - Origin Request Function

Reference: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-examples.html#lambda-examples-redirecting-examples

## AWS Global Accelerator

* Designed to optimize the flow of data from your users to your AWS Infrastructure.
* It does not Cache anything.

**Architecture**

![Global Content Delivery and Optimization-08-03-2024-8](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-8.png)

* It starts with 2 Anycast IP Addresses.
* Internet core routers will route traffic to the device closest to the source.
* Traffic initially uses the public internet and enters a Global Accelerator Edge Location.
* From the edge, data transits globally across the AWS global backbone network. Less hops, directly under AWS control, and significantly better performance. 

Anycast IP Address
	\- special type of IP address.
	\- allows a single IP to be in multiple locations.
	\- 1.2.3.4 & 4.3.2.1

Unicast IP Address
	\- refer to only 1 thing which is 1 network device.

![Global Content Delivery and Optimization-08-03-2024-9](images/Global%20Content%20Delivery%20and%20Optimization-08-03-2024-9.png)

