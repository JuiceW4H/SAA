# High Availability and Scaling (ELB, ASG…)

## Regional and Global AWS Architecture

* Global Service Location and Discovery
* Content Delivery (CDN) and Optimization
* Global Health Checks and Failover.
* Regional Entry Point
* Scaling & Resilience
* Application Services and Components.

**Global Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024.png)**
**
**
**
* Globally DNS is used for service discovery, regional based health checks and request routing.
* Content Delivery Networks (CDN) (CloudFront) are used to cache content globally even as close to end users as possible to improve performance.**
**

Region Architecture
![High Availability and Scaling (ELB, ASG…)-07-28-2024-1](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-1.png)

Tiers
- different zones of your application.

Web Tier
- act as an entry point for your regional based applications or application components.

Compute Tier
- provides functionality to the web tier.

Storage Tier
- will be consumed by the compute tier which essentially acts as a file storage.

Database Tier
- provides data storage for applications.

Caching Layer
- minimizes read operations to the DB.

## Evolution of the Elastic Load Balancer

* Load balancers are split between V1 (Avoid/Migrate From) and V2 (Preferred).

**3 Different Types of ELBs in AWS**
**
**
1. Classic Load Balancer (CLB) (V1) (Don’t Use)
	- can load balance HTTP and HTTPS as well as lower level protocols.
	- only supports 1 SSL Certificate per CLB.
	- lacks functionality of the V2 LBs.

1. Application Load Balancer (ALB) (V2)
	- true Layer 7 (Application Layer) Devices.
	- supports HTTP, HTTPS, and Web Socket Protocols.

1. Network Load Balancer (NLB) (V2)
	- supports TCP, TLS (Secure Form of TCP), and UDP Protocols.
	- for applications which don’t use HTTP or HTTPS (e.g. Email Servers, SSH Servers, etc).

* V2 LBs are faster and support target groups and rules, which allow you to use a single LB for multiple things.

## Elastic Load Balancer Architecture

* It’s the job of the Load Balancer to accept connections from customers and then to distribute those connections across any back-end compute.

**ELB Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-2](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-2.png)**
**

* IPv4 only or Dual Stack (IPv4 and IPv6).
* Pick the AZs (2 or more AZs) the LBs will use.
* When you provision a LB, the product places into these Subnets one or more LB Nodes.
* Only pick 1 Subnet in each AZ.
* When an LB is created, it gets created with a single DNS record (A Record) which points at all of the ELB nodes.
* LBs need 8 or more free IP addresses in the Subnets.
* AWS recommends to use a /27 or larger Subnet to allow for scaling.

Internet Facing
- nodes of that LB are given Public addresses and Private addresses.
- can connect to both Public and Private instances.

Internal Facing
- nodes only have Private IP addresses.
- for separating different tiers of applications.

Listener Configuration
- a configuration of what protocols and ports will be accepted.
- also communicates with targets on a port and protocol.

**Multi Tiered Application Architecture**

![High Availability and Scaling (ELB, ASG…)-07-28-2024-3](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-3.png)

* LBs allow each tier to scale independently of one another.

**Architecture Without Cross-Zone Load Balancing**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-4](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-4.png)**
**
**
**
**Architecture With Cross-Zone Load Balancing**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-5](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-5.png)**
**

Cross-Zone Load Balancing
- this feature simply allows every LB node to distribute any connections it receives equally across all registered instances in all AZs.
- when deploying an Application Load Balancer this feature is enabled by Default.

**ELB Architectural Points**
**
**
* ELB is a DNS A Record which points at 1 or more Nodes per AZ.
*  Nodes (in 1 Subnet per AZ) can scale.
* Internet-Facing means nodes are allocated with both Public and Private IPv4 Addresses.
* Internal means nodes only have Private IPv4.**
**
* EC2 does not need to be Public for it to work with LB.
* Listener Configuration controls what the LB does.
* LB requires 8+ Free IPs per Subnet, and /27 Subnet to allow scaling.

## Application Load Balancing (ALB) vs Network Load Balancing (NLB)

**Classic Load Balancer Architecture Sample**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-6](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-6.png)**
**
* CLBs don’t scale.
* Every unique HTTPS name requires an individual CLB because SNI isn’t supported.

**
**
**Application Load Balancer Architecture Sample**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-7](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-7.png)**
**
**
**
* Each Listener-Based rules can have an SSL Certificate handling HTTPS for all domains (1 SSL per Rule).
* v2 Load Balancers support Rules and Target Groups.
* Host based rules using SNI and an ALB allows consolidation.**
**

**Application Load Balancer**
**
**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-8](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-8.png)**
**
**
**
* A true Layer 7 LB and it’s configured to listen on HTTP/HTTPS Protocols (only Layer 7).
* Utilizes only HTTP/HTTPS listeners.
* Can inspect Layer 7 Content (Type, Cookies, Custom Headers, User Location, and Application Behavior) and make decisions based on that information.
* HTTP/HTTPS (HTTP w/ SSL/TLS) is always terminated on the ALB meaning it can’t do end-to-end SSL Encryption.
* A new connection is then made from the ALB to the application.
* ALBs must have SSL Certificates if HTTPS is used.
* ALBs are slower than NLB due to more levels of the network stack to process.
* Can evaluate application health at Layer 7.
* Can also route and load balance gRPC traffic.
* Does not support Static IPs.

Rules
- direct connections which arrive at a Listener.
- processed in Priority Order (Priority to Default Rule (Catchall or Last Rule Processed).

Rule Conditions
- Checking of the following:
	* host-header
	* http-header 
	* http-request-method
	* path-pattern
	* query-string
	* source-ip

Actions
- things that the rules do with the traffic.
- Available actions:
	* forward
	* redirect
	* fixed-response
	* authenticate-oidc
	* authenticate-cognito

Listener Rule
- defines how incoming traffic on a specific port should be handled by the load balancer.

Host Rule/Condition
- used to route traffic based on the host header of an incoming HTTP request.

Path Condition
- allows you to route a client request based on the URL path of the HTTP header.

* You can perform decisions based on anything which you can observe at Layer 7.

**Network Load Balancer**
**
**
* Functions at Layer 4 which means it can interpret TCP, TLS, UDP, and TCP_UDP.
* No visibility or understanding of HTTP/HTTPS.
* Really fast (Millions of Requests per second, and 25% of ALB Latency)
* Ideal for non HTTP/HTTPS Protocols.
* Health checks just check Internet Control Message Protocol (ICMP) / TCP Handshake.
* NLBs can have Static IP’s (useful for whitelisting).
* Can forward TCP to instances (end-to-end encryption).
* Used with PrivateLink to provide services to other VPCs.

**Quick Comparison of ALB vs NLB**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-9](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-9.png)

## Launch Configuration and Launch Templates

* They allow you to define the configuration of an EC2 instance in advance.
* They are not editable once defined.
	* Launch Templates has versions while Launch Configuration does not.
* Launch Templates have additional features including unlimited CPU options, Placement Groups, Capacity Reservations, and Elastic Graphics.
* AWS recommends the use of Launch Templates as they are a superset of Launch Configurations.

Following can be Configured in LC/LT:

* AMI
* Instance Types
* Storage
* Key Pair
* Networking
* Security Groups
* Userdata
* IAM Role

**LC and LT Architecture**

![High Availability and Scaling (ELB, ASG…)-07-28-2024-10](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-10.png)

Auto-Scaling Groups
- offer automatic scaling for EC2 instances.

Launch Configuration
- used as part of an Auto-Scaling Group (ASG).
- provide the configuration of the EC2 instance that will be launched by ASG.
- no versioning capabilities.

Launch Templates
- same task LC does, but can also be used to provision EC2 Instances from the AWS Console/CLI.

## Auto-Scaling Groups

* Provides Auto-Scaling and Self-Healing for EC2.
* Use one Launch Template or Launch Configuration at a time.
* Keeps the running instances at the Desired Capacity by provisioning or terminating instances.
* Define where instances are launched as they are linked to a VPC’s Subnets.

**3 Important Values to an ASG**
**
**
1. Minimum**
**
2. Desired
3. Maximum**
**

Scaling Policies
- can update the desired capacity based on certain criteria.
- rules that you define which can adjust the values of the ASG.

Instance Warm Up Time
- the number of seconds that it takes for a newly launched instance to warm up.

**3 Ways to Scale in ASG **

1. Manual Scaling
	- where you manually adjust the value of the Desired Capacity.

1. Scheduled Scaling
	- adjusts Desired Capacity based on schedules.

1. Dynamic Scaling
	- rules that react to something and changes the Desired Capacity.

	* Simple Scaling
		- a pair of rules (One to Provision and One to Terminate) (e.g. CPU above 50% +1", "CPU Below 50 - 1)

	* Step Scaling
		- acts depending on how out of normal the value is.
		- good for reacting on to quicker shifts in conditions.

	* Target Tracking
		- lets you define an ideal amount of something and maintains it within that metric.

* Predictive Scaling is Homogenous and inefficient/inaccurate if using a mix of Instance sizes/types.

Cooldown Period
- value in seconds
- 300s by Default.
- controls how long to wait at the end of a scaling action before doing another.

**Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-11](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-11.png)

Self-Healing
- when an instance gets replaced by ASG for not passing the Health/Status Checks.

**ASG + Load Balancer**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-12](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-12.png)

* ASG instances are automatically added to or removed from the target group.
* ASG can use Load Balancer health checks rather than EC2 status checks.

Scaling Processes

* Processes can be set to Suspend or Resume
* Launch pertains to the ability of the ASG to launch new instances.
* Terminate pertains to the ability of the ASG to terminate instances.
* AddToLoadBalancer refers to if instances are automatically added to LB Target Groups upon creation.
* AlarmNotification is for reacting to CW alarms/notification.
* AZRebalance is to balance instances evenly across all of the AZs.
* Health Check
* ReplaceUnhealthy is for terminating and replacing unhealthy instances.
* ScheduledActions is for whether or not ASG will perform scheduled actions.
* You can set an instance to either be on Standby or InService.

Standby
- suspends any ASG activities on a specific instance.

**Key Concepts**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-13](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-13.png)**
**

## ASG Scaling Policies

* ASG’s don’t need scaling policies to work, but they will only have static values.

Simple Scaling
- you define actions which occur when an alarm moves into an alarm state.
- inflexible.

Step Scaling
- increases or decreases the desired capacity based on a set of step adjustments that vary based on the size of the alarm breach.
- allows you to adjust better to changing load patterns.

Target Tracking
- you define an ideal value and ASG keeps the metric at the value that you want and adjusts the capacity to attain that.

Scaling Based on SQS
- increase and decrease capacity based on approximate number of messages visible (ApproximateNumberOfMessagesVisible).

**Simple Scaling Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-14](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-14.png)

* You are adding or removing the same amount no matter how extreme the increases or decreases in the metric you are monitoring.

**Step Scaling Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-15](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-15.png)**
**
**
**
* You have more flexibility as you can add or decreases based on increments or decrements of the metric you are monitoring.**
**

**Termination or Scale In Behavior:**
**
**
* Choose the Availability Zone with the most instances, ensuring at least one is unprotected from scale in. If multiple zones qualify, select the one with the oldest launch template.
* Identify the unprotected instances in the selected zone using the oldest launch template. If only one, terminate it.
* If multiple instances qualify for termination, select the unprotected one closest to the next billing hour to optimize EC2 usage costs. If only one, terminate it.
* If multiple unprotected instances are equally close to the next billing hour, terminate one at random.**
**

## ASG Lifecycle Hooks

* Lifecycle hooks allow you to configure custom actions which can occur during ASG actions.
* You can define actions which occur during Instance Launch transitions or Instance Terminate transitions.
* Instances are paused within the Launch or Terminate flow and wait until 1 of 2 things happen.
	* Configurable Timeout (Default is 3,600 seconds) which when it expires they will either Continue or Abandon the ASG action.
	* You can explicitly resume the process using CompleteLifecycleAction once you’ve performed the activity you wanted.
* Can be integrated with EventBridge or SNS Notifications, which allow your systems to perform event driven processing based on Launch or Termination of EC2 Instances.

**Lifecycle Hooks Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-16](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-16.png)

## ASG Health Check Comparison - EC2 vs ELB

* ASGs assess the health of instances using Health Checks and if an instance fails it is replaced by the ASG.

 
**3 Different Types of Health Checks**
**
**
1. EC2 (Default)
2. Stopping, Stopped, Terminated, Shutting Down or Impaired (not 2/2 status) equals to “Unhealthy”.

1. ELB (Can be Enabled)
	- “Healthy” status requires the instance to be running and passing the ELB Health Check.
	- checks could be application aware.

1. Custom
	- instances marked Healthy & Unhealthy by an External System.

Health Check Grace Period
- 300s by Default
- a configurable value that is basically the duration of delay before a Health Check occurs on a specific instance.

## SSL Offload and Session Stickiness

**3 Ways a Load Balancer Handles Secure Connections**
**
**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-17](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-17.png)**
**
**
**
1. Bridging (Default) (ALB)
	- one or more clients makes one or more connections to a LB.
	- Listener is configured for HTTPS.
	- SSL connections occur between the client and the LB.
	- LB and EC2 will require SSL certificates.

* Connection is terminated on the ELB & needs a certificate for the domain name.
* ELB initiates an SSL connection to the back end compute resources.

**
**
1. Pass-through (NLB)
	- the client connects, but the LB just passes the connection along to one of the back end instances.
	- LB does not decrypt the traffic.
	- EC2 instances still need SSL certificates
	- Listener is configured for TCP.

1. Offload**
**
	- clients connect to the LB using HTTPS.
	- connections are terminated on the LB.
	- from LB to back end HTTP connection is used instead meaning the traffic is unencrypted.
	- EC2 instances don’t have SSL certificates only on LB.

**Connection Stickiness**

* Without Stickiness connections are distributed across all in-service backend instances.
* Unless application handles user state this could cause user logoffs shopping cart losses.
* Session Stickiness can be enabled on a Target Group.
* If Enabled, the first time that user makes a request the LB generates a cookie called AWS ALB. This AWS ALB has a duration that you can define when enabling the feature.
* Duration could be as short as 1 second up to 7 days.
* Stickiness generates a cookie which locks the device to a single back end instance for a duration.
* Session Stickiness can be affected by server failure or cookie expiration.

## Gateway Load Balancer

* Helps you run and scale 3rd Party Security Appliances (Firewalls, Intrusion Detection and Prevention Systems).
* Performs Inbound and Outbound transparent traffic inspection or protection.

2 Major Components

1. Gateway Load Balancer Endpoints
	- runs from a VPC where the traffic you to monitor, filter, or control originates from or is destined to.

1. Gateway Load Balancer
	- load balances packets across multiple back end instances.

* Needs to forward packets without any alterations.

GENEVE
- traffic and metadata is tunneled using this protocol.

**GWLB 101 Architecture**
![High Availability and Scaling (ELB, ASG…)-07-28-2024-18](images/High%20Availability%20and%20Scaling%20(ELB,%20ASG…)-07-28-2024-18.png)

