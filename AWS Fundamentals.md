## AWS Public vs. Private Services

* AWS Public or Private services is a concept with regard to how a service is accessed. 
* VPCs by default cannot communicate with each other unless allowed to do so.
* VPCs can be accessed by on-premise networks through Virtual Private Networks or Direct Connect.

3 Network Zones from an AWS Perspective:  
1. Internet Public Zone
2. AWS Public Zone
3. AWS Private Zone

Public AWS Services
	\- pertains to services that can be access through public endpoints.

Private AWS Services
	\- services that require a Virtual Private Cloud (VPC) to work.

**AWS Structure**

![AWS Fundamentals-06-08-2024](images/AWS%20Fundamentals-06-08-2024.png)

## AWS Global Infrastructure

* Some services act per region some are on a global scale.

**Global Level Deployments**:

* AWS Regions
	\- an area where a deployment of AWS infrastructure is at (e.g. compute services, databases, AI).
	\- composed of at minimum of 3 availability zones.

* AWS Edge Locations
	\- smaller than regions.
	\- only have content distribution services and some types of edge computing.
	\- efficient data transfer due to caching.

**3 Main Benefits of having Regions**:

1. Geographic Separation - Isolated Fault Domains
2. Geopolitical Separation - Different Governance
3. Location Control - Performance

Availability Zones
	\- isolated physical facilities (data center/s) within a region.

![AWS Fundamentals-06-08-2024-1](images/AWS%20Fundamentals-06-08-2024-1.png)

### Service Resiliency: 

* Globally Resilient Services
	\- a service operates globally with a single database and its data is replicated across multiple regions inside AWS.
	\- even if a region fails the service continues.
	\- no concept of picking a region.

* Region Resilient Services
	\- operate in a single region with one set of data per region.
	\- operate as separate services in each region.
	\- generally replicates their data within multiple availability zones inside that region.
	\- if region as a whole fails the service will fail.

* Availability Zone (AZ) Resilient Services
	\- services run from a single availability zone.

## AWS Default Virtual Private Cloud (VPC)

**Virtual Private Cloud**
	\- a virtual network inside AWS.
	\- regionally resilient service.
	\- can only have a maximum (Soft Limit) of 5 VPCs per region (limit can be increased upon \- private and isolated unless you decided otherwise.

**2 Types of VPCs**

1. Default VPCs
	\- only 1 per region.
	\- configured always the same way.
	\- CIDR range is always 172.31.0.0/16
	\- /20 subnet per AZ in that region.

![AWS Fundamentals-06-08-2024-2](images/AWS%20Fundamentals-06-08-2024-2.png)

2. Custom VPCs
	\- can be configured end-to-end in detail.
	\- 100% private by default.

## Elastic Compute Cloud (EC2) Basics

* It is an Infrastructure as a Service (IAAS)
* It provides access to Virtual Machines or instances of VMs.
* Private service by default and it uses VPC networking.
* AZ resilient service
* One can choose the specifications of the VM (e.g. RAM, Storage Size, CPU).
* On-Demand billing (per second).
* Local on-host storage or Elastic Block Store (EBS).

EC2 States:

* Even if instance is in a stopped state EBS is still billed as it is still allocated to the instance.

1. Running
2. Stopped
3. Terminated

**Amazon Machine Image (AMI)**

* Permission Types:
	* Public
	* Owners are Implicitly allowed to create instances of that AMI.
	* Explicit permissions

* Essentially templates for instances
* AMI owner is implicitly allowed to create EC2 instances.
* Contains boot volumes and typical file storage or block device mapping.

Windows Remote Desktop Protocol = Port 3389
Linux Instances SSH Protocol = Port 22	  
* Private Keys are kept by the user.
* Public Keys are kept by AWS in the instance.

## Simple Storage Service (S3)

* It is an AWS public service as it can be accessed anywhere that has an internet connection.
* Global Storage Platform / Default Storage Service in AWS.
* Region Resilient Service
* It has the ability as well to replicate data between regions.

![AWS Fundamentals-06-08-2024-3](images/AWS%20Fundamentals-06-08-2024-3.png)

![AWS Fundamentals-06-08-2024-4](images/AWS%20Fundamentals-06-08-2024-4.png)

Objects (Files)
	\- the data contained within the S3 Buckets.

2 Components of an Object

1. Object Key (File Name)
2. Value (Content)

![AWS Fundamentals-06-08-2024-5](images/AWS%20Fundamentals-06-08-2024-5.png)

Buckets (Directory/Drives)
	\- created in a specific AWS region.
	\- bucket name should be globally unique.
	\- can hold unlimited number of objects.
	\- flat structure
	\- folders are termed as prefixes in S3.

![AWS Fundamentals-06-08-2024-6](images/AWS%20Fundamentals-06-08-2024-6.png)

Amazon Resource Name (ARN)
	\- unique identifiers for AWS services.

## CloudFormation Basics

* A tool that let’s you create, update, and delete infrastructure in AWS in a consistent and repeatable way using templates.
* Templates are in YAML or JSON

Sections of a template: 

1. Resources
	\- is a mandatory part of the template.
	\- creates, updates, and deletion of resources.

2. Description
	\- a free text field that let’s that author of the template add an description.
	\- if you have a description and an “AWSTemplateFormatVersion” then the Description must be placed after the Format Version.

![AWS Fundamentals-06-08-2024-7](images/AWS%20Fundamentals-06-08-2024-7.png)

3. AWSTemplateFormatVersion
	\- is the way that AWS allow for extending the standards overtime.
	\- if value is omitted it is assumed.

4. Metadata
	\- it can control how can the different things in the CloudFormation template are presented through the console UI if you are applying it.
	\- can control order, groupings, descriptions, labels, and etc. 

![AWS Fundamentals-06-08-2024-8](images/AWS%20Fundamentals-06-08-2024-8.png)

5. Parameters
	\- where you can add fields that prompts user more information.
	\- they are essentially the drop downs in the AWS Console during service creation.

![AWS Fundamentals-06-08-2024-9](images/AWS%20Fundamentals-06-08-2024-9.png)

6. Mappings
	\- it allows you to create lookup tables.
	\- providing preset of values depending on the scenarios (e.g. default value for a specific parameter depending on the region).

![AWS Fundamentals-06-08-2024-10](images/AWS%20Fundamentals-06-08-2024-10.png)

7. Conditions
	\- set certain things within a template when certain conditions are met.

![AWS Fundamentals-06-08-2024-11](images/AWS%20Fundamentals-06-08-2024-11.png)

8. Outputs
	\- it can present outputs based on what is created, updated, or deleted.
	\- possible return values from the template.

![AWS Fundamentals-06-08-2024-12](images/AWS%20Fundamentals-06-08-2024-12.png)

9. Transform
	\- 

## CloudWatch

* Collects and manages operational data
* Collections of metrics, monitoring of metrics, or actions based on metrics.
* It is a public service which can be used for metric gathering either inside AWS, within on-premises environments, or other cloud platforms.
* Can be accessed through UI (AWS Console), Command Line Interface, or API.

Default Metrics:

* CPU utilization
* Network utilization
* Disk performance
* Disk Reads/Writes

![AWS Fundamentals-06-08-2024-13](images/AWS%20Fundamentals-06-08-2024-13.png)

Metrics
	\- data relating to AWS products, applications, or on-premises systems.
	\- some metrics are gathered natively
	\- some require an extra piece of software called the CloudWatch Agent
	\- a collection of related data points in a time ordered structure (e.g. CPU utilization, Disk utilization, etc.).

Scenarios that require CloudWatch Agent: 

* Services outside AWS
* Other Cloud Environments
* Certain things inside AWS products that aren’t exposed to AWS (e.g. processes running in an EC2 instance, etc).

CloudWatch Logs
	\- collections of metrics based on logging data.
	\- requires CloudWatch Agent for logging of services outside AWS.

CloudWatch Events
	\- generates events/actions based on other events.
	\- generates events to on specific times of the day or on certain days of the weeks. 

Namespace
	\- container for monitoring data.
	\- has a name and it can be anything as long as it stays within the rule set of namespace names.
	\- all AWS data goes into an AWS namespace “AWS/<\service>” (e.g. AWS/EC2 - EC2 data namespace) which is reserved for AWS services.
	- group of metrics that can be used within that specific AWS service in the CloudWatch.

Datapoints
	- it consists of a timestamp of the when the measurement is conducted and a value of that specific metric.

Dimension
	- it separates datapoints for different things or perspectives within the same metric.
	- specifier with regards to what type of instance it came from and its possible ID (instance ID, type ,etc.).

Alarms
	- allows us to take actions based on metrics.
	- are created and linked to a specific metric then based on the configured alarm it will take an action based on that metric


Alarm States:
* OK - no problems found.
* ALARM - metric is not in a good state.
* INSUFFICIENT_DATA - alarm is still gathering data in order to go to an OK or ALARM state.

## Shared Responsibility Model

![AWS Fundamentals-06-08-2024-14](images/AWS%20Fundamentals-06-08-2024-14.png)

* Part of the responsibility lies within the vendor and another part relies on the responsibility of the customer.
* AWS is responsible for the Security “Of” The Cloud.
* Customer is responsible for Security “In” The Cloud. 

![AWS Fundamentals-06-08-2024-15](images/AWS%20Fundamentals-06-08-2024-15.png)

## High Availability vs Fault-Tolerance vs Disaster Recovery

**High Availability**
	\- aims to ensure an agreed level of operational performance usually uptime, for a higher than normal period.
	\- a system designed so that when it fails, its components can be replaced or fixed as quickly as possible often using automation to bring systems back into service.
	\- maximizing a systems online time.
	\- requires redundant resources to allow switching in the case the initial resources fails.
	\- accomplished by having spare equipment basically.

Gist: Minimize any outages

**Fault-Tolerance**
	\- the property that enables the system to continue operating properly in the event of the failure some (one or more faults within) of its components.
	\- minimize outages (same with High Availability), but also design the system to be able to tolerate failure (levels of system redundancy).

Gist: Operate through Faults

**Disaster Recovery**
	\- a set of policies, tools and procedures to enable the recover or continuation of vital technology infrastructure and systems following a natural or human-induced disaster.
	- designed to keep the crucial and non-replaceable parts of your system safe.

Gist: Used when HA and FT don’t work.

## Route53 Fundamentals

* Allows you to register domains
* Hosts zone files on managed nameservers
* Global service with a single database
* Globally resilient service
* There are 13 DNS Root Servers that exist managed by 12 Large Organizations.

**Register Domains**

* It has relationships with all of the major domain registries.
* IANA manages the root zone for DNS.
* Registries manages different zones (e.g. one manages the .com zone, .net zone and etc.).
* Route53 creates a Zone File for the domain being registered after checking if the domain is available in that registry.
* Route53 allocates a name servers for the zone.
* 4 name servers per one individual zone.

![AWS Fundamentals-06-08-2024-16](images/AWS%20Fundamentals-06-08-2024-16.png)

Registry
	- maintains the zones for TLD.

Hosted Zone/s (Zone Files)
	- a database that contains all of the DNS information for a particular domain.
	- hosted on 4 managed name servers.
	- it can be public or live logically in the AWS public zone which is accessible anywhere.
	- it can also private and linked through VPCs and accessed only through those VPCs.
	- it stores records or “recordsets”.

Transfer Lock
	- registered domains that has this option enabled cannot be transferred away from Route53

## DNS Record Types

![AWS Fundamentals-06-08-2024-17](images/AWS%20Fundamentals-06-08-2024-17.png)

**Nameserver (NS)**
	\- allow delegation to occur in DNS.
	\- specify which which DNS servers are responsible for providing the information about a Domain.

![AWS Fundamentals-06-08-2024-18](images/AWS%20Fundamentals-06-08-2024-18.png)

**A Record**
	- maps the host to IPv4 address.

**AAAA Record**
	- maps the host to an IPv6 address.

![AWS Fundamentals-06-08-2024-19](images/AWS%20Fundamentals-06-08-2024-19.png)

**Canonical Name (CNAME)**
	- lets you create the equivalent of DNS shortcuts.
	- host to host records.
	- used to reduce admin overhead.
	- cannot point directly at an IP address only other names.

![AWS Fundamentals-06-08-2024-20](images/AWS%20Fundamentals-06-08-2024-20.png)

**MX Record**
	- how a server can find the mail server for a specific domain.

**2 Parts of an MX Record**

1. Priority
	\- to choose which record to use.
	\- lower value in the priority field means higher priority.

2. Value
	\- can be just a host.

![AWS Fundamentals-06-08-2024-21](images/AWS%20Fundamentals-06-08-2024-21.png)

**Text (TXT) Record**
	\- allows you to add arbitrary text to a domain.
	\- a way in which the DNS system can provide additional functionality.
	\- common usage is for proving domain ownership.

![AWS Fundamentals-06-08-2024-22](images/AWS%20Fundamentals-06-08-2024-22.png)

DNS Time to Live (TTL)
	- a numeric value in seconds.