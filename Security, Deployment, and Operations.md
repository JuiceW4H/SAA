# Security, Deployment, and Operations

## AWS Secrets Manager

* Designed specifically for Secrets (Passwords, API Keys, etc).
* Usable via Console, CLI, API, and SDK (Integration).
* Supports automatic rotation of Secrets through Lambda.
* Directly integrates with some AWS Services/Products.

**Secrets Manager Architecture**
![Security, Deployment, and Operations-08-07-2024](images/Security,%20Deployment,%20and%20Operations-08-07-2024.png)

* Secrets are encrypted using KMS.
* Lambda can be invoked periodically to rotate Secrets.
* Products are like RDS also kept in Sync.

## Application Layer (L7) Firewall

Normal Firewalls
![Security, Deployment, and Operations-08-07-2024-1](images/Security,%20Deployment,%20and%20Operations-08-07-2024-1.png)

* Layer 3 and 4 Firewall sees packets, segments, IP addresses, and ports.
* It views it as 2 flows of communications (Request and Response).
* The flows are viewed as separate.
* In Layer 5, the Firewall views the 2 flows of communication as 1 Session.

Layer 7 Firewalls
![Security, Deployment, and Operations-08-07-2024-2](images/Security,%20Deployment,%20and%20Operations-08-07-2024-2.png)

* Can identify normal and abnormal request.
* Aware of L7 Protocols (e.g. HTTP).
* Data at L7 can be inspected, blocked, replaced, or tagged.
* Able to identify, adjust, or block specific applications.
* L7 Firewall keeps all L3, L4 & L5 features, but can react to L7 elements (DNS, RATE, Content, Headers etc).

## Web Application Firewall (WAF)

**WAF Architecture**
![Security, Deployment, and Operations-08-07-2024-3](images/Security,%20Deployment,%20and%20Operations-08-07-2024-3.png)

* Unit of configuration within WAF is the Web ACL (WACL).
* Capable of protecting Global and Regional Services.
* Logs can be directed to S3 (5 minute delay), CW Logs, or Kinesis Firehose.

Web ACL
- is what controls if traffic is allowed or blocked.
- has a default action.
- a Web ACL is created for either CloudFront or a Regional Service.
- things that which are associated with resources.

* Associating Web ACL to a resource takes time.
* Adjusting a Web ACL that is already associated takes less time.
* A resource can only have 1 Web ACL, but a Web ACL can be associated with many resources.

Default Action
- will either allow or block any traffic which isn’t matched by the ACL.

* Rule Groups or Rules are processed in order.
* Default actions are defined when groups or rules are added to Web ACLs.

Web ACL Capacity Units (WCU)
- an indication of the complexity of rules.
- has a Default maximum of 1,500 WCU (Soft Limit).

Rule Groups
- groups of rules.
- Managed (AWS or Marketplace), Customer Managed, or Service Owned.
- can be used by multiple Web ACLs.

* When you create a Rule Group you define upfront the WCU capacity (Defined Upfront)(Maximum is 1,500 WCU).

Rules
- consists of a type, statement, and action.

Type
- determines how it works.

Statement
- consists of one or more things which match traffic or not.
- it defines what the rules checks for.
- Regular (What to Match) and Rate-based (Rate Limit or Count All)(What and Count).

* You can have more than 1 Statement which could be connected by AND, OR, & NOT Conditions.

Action
- is what WAF does if a match occurs.
- Regular: Allow, Block, Count, or run a Captcha.
- Rate-based: Block, Count, or run a Captcha.

* Blocks can have a Custom Response or a Custom Header.
* Allow, Count, and Captcha can only have a Custom Header.

Custom Header
- means you application can react to traffic which has been matched.
- prefixed with x-amzn-waf-*.

Labels
- can optionally be added.
- allows for multi-stage flows.
- internal to WAF only.
- can be reference from other rules within a single Web ACL.

* Using Labels relies on WAF not stopping processing.
* Allow and Block actions results no further action if a rule matches.
* Count and Captcha action processing continues.

**2 Types of WAF Rules**

1. Regular Rules
	- designed to match if something occurs.

1. Rate-based Rules
	- designed to match if something occurs at a certain rate.

**Pricing**

* Charged a monthly price for every Web ACL ($5/month).
* Charge per Rule on a Web ACL ($1/month).
* Charge for every Rule Group.
* Charge for every request processed per Web ACL ($0.60/1 Million Requests).

**Optional Security Features**

* Bot Control - $10/month & $1/1 Million Request
* Captcha - $0.40/ 1,000 Challenge Attempts
* Fraud Control/Account Takeover - $10/month & $1/1,000 Login Attempts
* Marketplace Rule Groups - Extra costs.

## AWS Shield

* An essential tool to protect any internet connected environment from DDoS.

**2 Forms of AWS Shield**
**
**
* Both provide DDoS protection.**
**

**
**
1. Shield Standard
	- free for AWS customers.

1. Shield Advanced
	- commercial extra product which comes with additional costs and benefits.

**Different Layers of DDoS Attacks**
**
**
1. Network Volumetric Attacks (Layer 3)
	- designed simply to overwhelm the system being attacked.
	- direct as much raw network data at a target as possible.

1. Network Protocol Attacks (Layer 4)
	- SYN Floods
	- generates a huge number of connections from a spoofed IP address and then just leave connections open.

1. Application Layer Attacks (Layer 7)
	- web request floods
	- using the application as intended where certain parts of the application are cheap to request, but expensive to deliver the response.

AWS Shield Standard

* Free for all AWS Customers.
* Protection is at the perimeter of the network which can either be in your region or at the edge of the AWS network (CloudFront or Global Accelarator).
* Protects against Layer 3 and Layer 4 attacks.
* Best protection is you use R53, CloudFront, and AWS Global Accelerator.

AWS Shield Advanced

* A commercial product which costs $3,000 per month (per Organization), with a 1 year lock-in and data out charge per month.
* Protects CF, R53, Global Accelerator, anything associated with Elastic IPs, ALBs, CLBs, and NLBs.
* Protections are not automatic as you need to explicitly enable it in Shield Advanced or AWS Firewall Manager Shield Advanced Policy.
* Cost protection for unmitigated attacks.
* Access to AWS Shield Response Team (SRT).

Features of Shield Advanced

* WAF Integration
* Layer 7 DDoS Protection using WAF.
* Provides Advanced Real-time metrics and reports.
* Health-based detection using application specific health checks (R53 Health Checks).

Protection Groups
- groupings of resources which Shield Advanced protects.
- you can define criteria of membership to add new resources automatically.

## AWS CloudHSM

![Security, Deployment, and Operations-08-07-2024-4](images/Security,%20Deployment,%20and%20Operations-08-07-2024-4.png)

* CloudHSM is essentially a true “Single Tenant” Hardware Security Module (HSM).
* Runs in 1 AZ only.
* AWS provisioned service but fully customer managed.
* Fully FIPS 140-2 Level 3 Compliant (KMS is L2 Overall).
* Accessible via Industry Standard APIs (e.g. PKCS#11, Java Cryptography Extensions (JCE), Microsoft CryptoNG (CNG) Libraries).
* KMS Can use CloudHSM as a Custom Key Store, CloudHSM integration with KMS.

**CloudHSM Architecture**
![Security, Deployment, and Operations-08-07-2024-5](images/Security,%20Deployment,%20and%20Operations-08-07-2024-5.png)**
**
**
**
* CloudHSMs are not deployed inside a VPC that you control, but instead AWS Managed CloudHSM VPC.
* HSMs by Default is not an HA device.
* Once HSM devices are configured as a cluster, they are able to replicate any keys, policies or other configurations in that cluster.
* Injected via ENIs in Customer VPCs.
* A client needs to be installed on EC2 instances to utilize the CloudHSM devices.
* AWS have no access to secure area where key material is held.

Use Case

* No Native AWS Integration between CloudHSM and any AWS products.
* Can be used to offload SSL or TLS processing from web servers.
* Enable Transparent Data Encryption (TDE) for Oracle Databases or MS SQL.
* Protect the private keys for an issuing Certificate Authority.

**## AWS Config**## 

![Security, Deployment, and Operations-08-07-2024-6](images/Security,%20Deployment,%20and%20Operations-08-07-2024-6.png)

* Primary function is to record changes over time on resources within an AWS Account.
* Every time a resource configuration changes, a configuration item is created which stores the configuration of that resource at a specific point in time.
* Information stored is the configuration of the resource, relationship to other resources and who makes any changes.
* Regional services and also supports Cross-Region and Cross Account Aggregation.
* Changes can generate SNS notifications and near-realtime events via EventBridge & Lambda.

Use Case
* Auditing changes
* Compliance with standards

Config Rules
- allow you to evaluate the configuration of your AWS resources.
- either AWS Managed or Custom Rules (using Lambda).

## Amazon Macie

* A service which can be used to discover, monitor, and protect data which is stored in S3 Buckets.
* Detects data that is classifiable as Personal Identifiable Information (PII), Personal Health Information (PHI), Finance, and other types of data.
* Uses Data Identifiers to assess against an Object inside a Bucket.

Data Identifier
- rules which your object and their contents is assessed against.

**2 Types of Data Identifiers**

![Security, Deployment, and Operations-08-07-2024-7](images/Security,%20Deployment,%20and%20Operations-08-07-2024-7.png)**
**
**
**
1. Managed Data Identifiers
	- built-in to Macie.
	- uses a combination of criteria and techniques (ML & Pattern Matching).

1. Custom Data Identifiers
	- uses proprietary data which your business needs to identify and control.
	- regex based.

Maximum Match Distance
- defines how close keyword are to a regex pattern.

* Integrates with Security Hub and EventBridge.
* Macie uses a multi-account architecture which is centrally managed via AWS Organizations or 1 Macie Account inviting.

Data Discover Job
- an automated process that scans data stored in Amazon S3 buckets to identify and classify sensitive information.
- has a schedule and frequency.
- uses a combination of the 2 type of data identifiers.

**Macie Architecture**
![Security, Deployment, and Operations-08-07-2024-8](images/Security,%20Deployment,%20and%20Operations-08-07-2024-8.png)**
**

**2 Types of Findings**
**
**
1. Policy Findings
	- generated when the policies or settings for an S3 bucket are changed after Macie is enabled.

1. Sensitive Data Findings
	- generated when it discovers sensitive data in S3 objects.

**## Amazon Inspector**

* Designed to check EC2 instances, OS, and Container workloads for vulnerabilities or deviations against best practice.
* Runs an assessment of varying lengths 15 minutes, 1 hour, 8/12 hours or 1 day.
* Generates a review of findings ordered by severity/priority.

**2 Main Types of Assessment**
**
**
1. Network Assessment
	- can be conducted without an agent.
	- having an agent provides richer information.

1. Network &  Host Assessment
	- requires agent.
	- looks at OS level vulnerabilities.

Rules Packages
- determines what is checked 

1. Network Reachability (No Agent Required)
	-  checks how an instance is exposed to public networks.
	- end-to-end reachability.

1. Common Vulnerabilities (CVE)
	- this package checks against those a database of known Cyber Security vulnerabilities.

1. Center for Internet Security (CIS) Benchmarks
	- provides well defined, unbiased, consensus based industry best practices which it checks against.

1. Security Best Practices for Inspector
	- a collection of best practices provided by Amazon

## Amazon GuardDuty

![Security, Deployment, and Operations-08-07-2024-9](images/Security,%20Deployment,%20and%20Operations-08-07-2024-9.png)

* A continuous security monitoring service once enabled.
* GuardDuty aims to identify any unexpected or unauthorized activity on the account.
* It learns patterns of what happens normally within any managed accounts.
* Supports multiple account via a Master and Member account architecture.

Master
- the account you enabled GuardDuty in.

Member
- accounts that accept invitation from the Master account become member accounts.

**GuardDuty Architecture**
![Security, Deployment, and Operations-08-07-2024-10](images/Security,%20Deployment,%20and%20Operations-08-07-2024-10.png)**
**
**
**
**Supported Data Sources**
**
**
* DNS Logs
* VPC Flow Logs
* CloudTrail Event Logs
* CloudTrail Management Events
* CloudTrail S3 Data Events

