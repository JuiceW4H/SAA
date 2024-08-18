## R53 Public Hosted Zones

![Route 53 - Global DNS-07-23-2024](images/Route%2053%20-%20Global%20DNS-07-23-2024.png)

* Route53 is a Globally Resilient (Multiple DNS Servers) service.
* A zone hosts DNS records(A, AAAA, TXT, & etc).

Hosted Zone
	\- databases which are reference via delegation using name server records.
	\- a DNS database for a given section of the global DNS database.
	\- created automatically when you register a domain using Route53.
	\- can also be created separately if you wanted to register a domain elsewhere.

* A monthly fee for to host each hosted zone and a fee for the queries made against that hosted zone.
* Hosted Zones are reference through name server delegation is authoritative for a domain.

Public Hosted Zone
	\- a DNS database (zone file) hosted by Route53 (Public Name Servers).
	\- accessible from the Public Internet and within VPCs using the Route53 resolver.
	\- R53 allocates 4 public name servers specific for the zone.
	\- to integrate it with the public DNS system, you change the name server records for that domain to point at those 4 R53 NS.

Resource Records
	\- actual items of that that DNS uses.

* You can use R53 to host zone files for externally registered domains.

**Public Hosted Zone Architecture**
![Route 53 - Global DNS-07-23-2024-1](images/Route%2053%20-%20Global%20DNS-07-23-2024-1.png)

## R53 Private Hosting Zones

* Operates in the same manner as a Public Hosted Zone, but Private.
* Accessible only with VPCs in AWS that it is associated with.
* You can associate a Private Hosted Zone with VPCs in your account using the Console, CLI, and API.
* For different accounts you use CLI, and API only.

Split-View or Split-Horizon DNS
	\- where you have public and private hosted zones of the same name.

**Private Hosted Zones Architecture**

![Route 53 - Global DNS-07-23-2024-2](images/Route%2053%20-%20Global%20DNS-07-23-2024-2.png)

**Split-View or Split-Horizon Hosted Zone**

![Route 53 - Global DNS-07-23-2024-3](images/Route%2053%20-%20Global%20DNS-07-23-2024-3.png)

## CNAME vs R53 Alias

![Route 53 - Global DNS-07-23-2024-4](images/Route%2053%20-%20Global%20DNS-07-23-2024-4.png)

CNAME
	\- basically a nickname/ shortcut which points to the Apex of the domain or Naked domain.

* A Record maps a name to an IP address (e.g. catagram.io => IP 1.3.3.7)
* CNAME maps a name to another name (e.g. www.catagram.io => catagram.io).
* CNAME is invalid for the Apex of a domain (catagram.io) or Naked Domain.

![Route 53 - Global DNS-07-23-2024-5](images/Route%2053%20-%20Global%20DNS-07-23-2024-5.png)

Alias Record
	\- generally maps a name onto an AWS resource.
	\- can be used for both the naked domain and normal records.
	\- there are no charges for Alias requests pointing at AWS resources.
	\- default to picking Alias records for anything in a domain where you’re pointing at an AWS resources.

* Should be the same “Type” as what the record is pointing at (e.g. A Record Alias to an A Record Type)
* Alias can only be used if R53 is hosting your domains.

## Simple Routing

![Route 53 - Global DNS-07-23-2024-6](images/Route%2053%20-%20Global%20DNS-07-23-2024-6.png)

* The default and simplest.
* You can create one record per name.
* Each record can have multiple values.
* All values are returned in a random order.
* Use when routing requests to a single service.

Limitations

* Does not support Health Checks (checks if the resource its pointing to is operational).

## R53 Health Check

![Route 53 - Global DNS-07-23-2024-7](images/Route%2053%20-%20Global%20DNS-07-23-2024-7.png)

* Separate from but are used by records in R53.
* Performed by a fleet of Health Checkers that are distributed Globally.
* They are not limited to AWS targets. You can check anything that is accessible over the Public Internet.
* Health Checks occur every 30 seconds (can be reduced to every 10 seconds - additional cost).
* Endpoint either is Healthy or Unhealthy.

**Connection Checks Through:**

1. TCP
	\- R53 tries to establish a TCP connection with the endpoint. 
	\- needs to be successful within 10 seconds.

2. HTTP/HTTPS
	\- R53 must be able to establish a TCP connection with the endpoint within 4 seconds.
	\- in addition the endpoint must respond with an HTTP status code in the 200 or 300 range within 2 seconds after connecting.

	* String Matching
		\- has the checks of previous with an additional check. 
		\- R53 Health Check receives a response body in the next 2 seconds which it then checks for the string that you specify.
		\- the string must appear entirely in the first 5120 bytes of the response body. **

**3 Types of Checks**

1. Endpoint
	\- assess the health of the endpoint that you specify.

2. CloudWatch Alarm
	\- react to CloudWatch alarm or some CloudWatch Agent metric you configured.

3. Calculator Checks
	\- check of other checks.

**R53 Health Check Architecture**

![Route 53 - Global DNS-07-23-2024-8](images/Route%2053%20-%20Global%20DNS-07-23-2024-8.png)

* If more than 18% of the health checkers report as health, then the health check is healthy otherwise unhealthy.
* An unhealthy record is not returned in response to queries.

## Failover Routing

![Route 53 - Global DNS-07-23-2024-9](images/Route%2053%20-%20Global%20DNS-07-23-2024-9.png)

* We can add multiple records with the same name (Primary and Secondary)
*  Each of these records points at a resource.
* A common architecture is to use Failover for a “out-of-band” failure/maintenance page for a service.
* The key element of Failover routing is the inclusion of a health check.
* If the health check of the primary record is healthy it is used if not it. The secondary value is used as backup.

**Multi Value Routing**

![Route 53 - Global DNS-07-23-2024-10](images/Route%2053%20-%20Global%20DNS-07-23-2024-10.png)

* Like a mixture between simple and failover.
* You can create many records all with the same name.
* Up to 8 healthy records are returned to the client (if more exist, 8 are randomly selected).
* Records that fail the Health Check won’t be returns to the client.
* Each records is independent and can have an associated Health Check.
* Used when you have many resources which can all service requests, and you want them all health checked and returned at random.

**Weighted Routing**

![Route 53 - Global DNS-07-23-2024-11](images/Route%2053%20-%20Global%20DNS-07-23-2024-11.png)

* Can be used when you’re looking for a simple form of load balancing, or when you want to test new versions of software.
* You are able to specify a weight for each record or Record Weight.
* A 0 weight means that it is never returned unless all are 0 then all are considered.
* Records with the same name are returned based on its weight over the total weight.
* Can incorporate health checks.
	* If a record is unhealthy the process of selection is repeated until a healthy record is returned.

## Latency-Based Routing

![Route 53 - Global DNS-07-23-2024-12](images/Route%2053%20-%20Global%20DNS-07-23-2024-12.png)

* Used when you’re trying to optimize for performance and user experience, when you want R53 to return records that provide better performance.
* For each of the records you can specify a record region.
* Supports 1 record with the same name in each AWS Region.
* AWS maintains a database of latencies between the users general location and the regions in records.
* The record returned is the lowest estimated latency and is healthy.

## Geolocation Routing

![Route 53 - Global DNS-07-23-2024-13](images/Route%2053%20-%20Global%20DNS-07-23-2024-13.png)

* Location of customer and location of resources are used to influence resolution decisions.
* When you create records you tag records with the location.
* Geolocation doesn’t return the closest records, it only returns relevant (location) records.
* Ideal for regional restrictions, language specific content, or load balancing across regional endpoints.

**Location Tags**

* Country (ISO Code)
* Continent (ISO Continent Code)
* Default
* Sub-Division or State

**Order of Resolution Returns**

* It returns the most specific record or no answer (If No Default).

1. State
2. Country
3. Continent
4. Default

## Geoproximity Routing

![Route 53 - Global DNS-07-23-2024-14](images/Route%2053%20-%20Global%20DNS-07-23-2024-14.png)

* Aims to calculate the distance between a customer and the record and answer with the lowest distance.
* You define the AWS region that an AWS resource is created in, or provide the Coordinates if it is an External resource alongside those 2 is also a bias (+ or -).
* + or - bias can be added to rules.
* + increases a region size.
* - decreases a region size.
* Routing is distance based but it includes this bias.

Bias
	\- expands or shrinks a size of a geographic region that is used for traffic to be routed to.

## R53 Interoperability

![Route 53 - Global DNS-07-23-2024-15](images/Route%2053%20-%20Global%20DNS-07-23-2024-15.png)

* Using R53 to register domains or to host zone files when the other part of that is not with R53.
* R53 normally acts as a Domain Registrar and Domain Hosting service.
* It can do Both, or either Registrar only, or Host only.

Domain Registration Fee
	\- once a year or once every 3 year fee.

Domain Hosting
	\- allocates 4 Name Servers (NS).
	\- creates a zone file on the NS.

Domain Registry
	\- company or entity responsible for the specific Top Level Domain.

Domain Registrar
	\- the company registering the domain on your behalf with the Domain Registry

**R53 on Both Roles**
![Route 53 - Global DNS-07-23-2024-16](images/Route%2053%20-%20Global%20DNS-07-23-2024-16.png)

**R53 as Registrar Only**

![Route 53 - Global DNS-07-23-2024-17](images/Route%2053%20-%20Global%20DNS-07-23-2024-17.png)

**R53 for Domain Hosting Only**

![Route 53 - Global DNS-07-23-2024-18](images/Route%2053%20-%20Global%20DNS-07-23-2024-18.png)

## Implementing DNSSEC using Route53

![Route 53 - Global DNS-07-23-2024-19](images/Route%2053%20-%20Global%20DNS-07-23-2024-19.png)

* Enabling DNSSEC in R53 is done from either the Route53 Console UI or the CLI, and once initiated the process starts with KMS.
* An Asymmetric Key Pair (Public and Private Key) is created within KMS which are used to create Key Signing Keys.
* Route53 creates and manages the Zone Signing Keys internally.
* Route53 then adds the KSK and the ZSK public parts into a DNSKEY record within the hosted zone.
* The Private KSK is used to sign those DNSKEY records, and create the RRSIG DNSKEY record.
* R53 has to establish a chain of trust with the parent zone (TLD) and add a Delegated Signer record. 
* Create CloudWatch alarms for DNSSECInternalFailure and DNSSECKeySigningKeysNeedingAction.
* Enable DNSSEC validation for VPCs.

Delegated Signer Record
	\- a hash of the Public KSK for this zone.

