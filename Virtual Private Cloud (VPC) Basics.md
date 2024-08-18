## VPC Sizing and Architecture

* A VPC minimum /28 (16 IP), maximum /16 (65536 IPs)
* One subnet per Availability Zone
* You cannot disable IPv4 support for your VPC and subnets since this is the default IP addressing system.

**VPC Considerations:**

* What size should the VPC be?
* Are there any networks that we can’t use?
* Be mindful of the ranges that other VPCs use, ranges which are utilize in other cloud environments, on other on-premises networks, and even partners and vendors.
* Try to predict the future.
* Consider the VPC Structure in terms of Tiers and Resiliency (Availability) Zones.
* Avoid common ranges to avoid future issues.
* Reserve 2+ networks per region being used per account**

**IP Ranges to Avoid:**

* 192.168.10.0/24 (192.168.10.0 -> 192.168.10.255)
* 10.0.0.0/16 (AWS) (10.0.0.0 -> 10.0.255.255)
* 172.31.0.0/16 (Azure) (172.31.0.0 -> 172.31.255.255)
* 192.168.15.0/24 (London) (192.168.15.0 -> 192.168.15.255)
* 192.168.20.0/24 (New York) (192.168.20.0 -> 192.168.20.255)
* 192.168.25.0/24 (Seattle) (192.168.25.0 -> 192.168.25.255)
* 10.128.0.0/9 (Google) (10.128.0.0 -> 10.255.255.255)

![Virtual Private Cloud (VPC) Basics-07-08-2024](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024.png)

**VPC Sizing**

![Virtual Private Cloud (VPC) Basics-07-08-2024-1](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-1.png)

**Best Practice**

* Subnet for 3 Availability Zones and assume an extra AZ if possible so a total of 4.
* Assume the Web, Application, Database, and Spare tiers.
* Use 10.16 -> 10.127 range (to avoid Google)

**Proposal**

![Virtual Private Cloud (VPC) Basics-07-08-2024-2](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-2.png)

## Custom VPCs

![Virtual Private Cloud (VPC) Basics-07-08-2024-3](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-3.png)

![Virtual Private Cloud (VPC) Basics-07-08-2024-4](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-4.png)

* VPCs are a Regional Service and regionally isolated.
* It operates from all of the Availability Zones in that Region.
* It allows you to create isolated networks inside AWS.
* Nothing is allowed IN or OUT of a VPC without explicit configuration.
* Let you create networks with almost any configuration (Simple or Multi-Tier).
* Supports Hybrid Networking which allows to connect to other cloud platforms & on-premises networks.
* Option of picking between Shared (Default) or Dedicated Tenancy.
* A VPC can use IPv4 Private CIDR Blocks and Public IPs.
* Allocated 1 Mandatory Private IPv4 CIDR Block (Primary Block or PB).
* PBs can be a /28 prefix (16IP) (Smallest) or /16 (65,536) (Maximum)
* A Soft Limit (Default) of 5 and a Hard Limit of 20.
* VPC can be configured to use IPv6 by assigning /56 CIDR Block to VPC.

IPv6
	\- range is either allocated by AWS (no choice on which range to use) or you can select to use your own IPv6 addresses (addresses you own).
	\- don’t have a concept of public or private.
	\- range of IPv6 addresses that AWS uses are all publicly routable by default, but still requires explicitly allow connectivity to and from the public internet.

Private IPs Addresses
	\- by default, everything uses these private addresses.

Public IPs or Addresses
	\- used for when you want to make resources public.
	\- when you want resources to communicate with the public internet or AWS Public Zone.
	\- for allowing communication to the resources from the Public Internet.

Default (Shared)
	\- you can choose on a per resource basis if it goes on shared hardware or dedicated hardware.
	\- initially shared hardware by Default.

Dedicated Tenancy
	\- any resources that you create inside this VPC option have to be on dedicated hardware.
	\- conceptually you have your own hardware in AWS data centers.

**DNS in a VPC**

* Provided by Route53.
* It’s available on Base IP of the VPC plus 2 (VPC Base IP +2 Address)(e.g. VPC 10.0.0.0 = DNS IP will be 10.0.0.2).

 **2 Critical Options for how  DNS Functions in a VPC:**

1. enableDnsHostnames
	\- gives instances DNS Names.
	\- indicates whether instances with Public IP Addresses in a VPC are given DNS host names.
	\- disabled by Default.

2. enableDnsSupport
	\- enables DNS resolution in VPC.
	\- indicates whether DNS is enabled or disabled in the VPC.
	\- enabled by Default for Custom VPCs.**

* Using a Bastion host is frowned upon and not a best security practice for getting access to AWS VPCs.

**VPC Design State End Sample (A4L Sample)**

![Virtual Private Cloud (VPC) Basics-07-08-2024-5](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-5.png)

**Account Demo VPC Architecture in this Lecture**

![Virtual Private Cloud (VPC) Basics-07-08-2024-6](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-6.png)

VPC Reference: https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html

## VPC Subnets

![Virtual Private Cloud (VPC) Basics-07-08-2024-7](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-7.png)

* Subnets are what services run from inside VPCs and they’re how you add structure, functionality, and resilience to VPCs.
* In AWS Diagrams, Blue means Private Subnets and Green means Public Subnets.
* Availability Zone resilient feature of a VPC meaning if an AZ fails a Subnet fails.
* One Subnet is created in a specific Availability Zone in that Region which can never be changed.
* A subnet can never be in multiple Availability Zones (AZs can have none, one, or multiple Subnets).
* IPv4 CIDR is a subset of the VPC CIDR.
* Cannot overlap with other Subnets.
* A Subnet can be optionally allocated an IPv6 CIDR (/64 subset of the /56 VPC - space for 256) as long as the VPC also is enabled for IPv6.
* Subnets inside of VPC can, by Default, communicate with other Subnets in that same VPC.

**Reserved IP Addresses (5 in Total)**

Example Subnet: 10.16.16.0/20 (Range: 10.16.16.0  - 10.16.31.255)

1. Network Address (10.16.16.0)
2. VPC Router - “Network +1” (10.16.16.1)
3. Reserved (DNS*) - “Network +2” (10.16.16.2)
4. Reserved Future Use - “Network +3” (10.16.16.3)
5. Broadcast Address (10.16.31.255) - Last IP in Subnet

Dynamic Host Configuration Protocol (DHCP) Option Set
	\- how computing devices receive IP addresses automatically.
	\- it controls DNS servers, NTP servers, net bio servers, and a few other things.
	\- you can create option sets, but you cannot edit them.
	\- if there is a need for modifying the existing one just create a new option set then change the VPC allocation to the new option set.

**2 Important IP Allocation Options**

* Both are defined at a subnet level and flows onto any resources inside that subnet.**

1. Auto Assign Public IPv4
	\- controls if resources in a Subnet are allocated a Public IPv4 Address in addition to that Private Subnet Address automatically.
	\- it’s on a per subnet basis that you can set auto assign Public IPv4 Addresses.

1. Auto Assign IPv6
	\- for it to work a Subnet has to have an allocation as does the VPC.

**Account Demo VPC Architecture in this Lecture**

![Virtual Private Cloud (VPC) Basics-07-08-2024-8](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-8.png)

## VPC Routing, Internet Gateway & Bastion Hosts

* A subnet can only have one Route Table associated with it, but a Route Table can be associated with multiple subnets. 
* 0.0.0.0/0 matches all IPv4 IP addresses (Default Route).
* The higher the prefix value the more specific the route is the higher the priority.
* A VPC can only have one IGW or no IGW and vice versa.

VPC Router
	\- a VPC router is a highly available device present in every VPC (Default or Custom).
	\- basically moves traffic from somewhere to somewhere else.
	\- it runs in all the Availability Zones that the VPC uses.
	\- has a network interface in every subnet which is the “Network +1” Address.
	\- controlled by Route Tables.
	\- looks at the destination address of all packets leaving the subnet, and when it has that address, it looks at the route table and identifies all of the routes that match the destination address.

Destination Field
	\- determines what destination the route matches.
	\- could match only one specific IP address (e.g. IP with a /32 prefix).
	\- could also be a network match or a default route.

Target Field
	\- will either point to the AWS gateway or it will say local.
	\- Local means that the destination is within the VPC itself.
	\- Local routes are always there, uneditable, and match the VPC IPv4 or IPv6 CIDR Range.
	\- for anything else, higher prefix values are more specific and they take priority.

* Local routes always take priority.

Packet
	\- has a source address and a destination address as well as some data.

Route Tables
	\- influences what to do with traffic when it leaves a subnet.
	\- defines what the VPC router will do when data leaves the subnet.
	\- a VPC is created with what’s known as a Main Route Table.
	\- if you don’t explicitly associate a Custom Route Table with a subnet it uses the Main Route Table.
	\- a list of routes.
	\- all route tables have at least one route, the local route.
	\- if VPC is IPv6 enabled, it will also have another local route matching the IPv6 CIDR VPC.

Internet Gateway
	\- regionally resilient gateway which can be attached to a VPC.
	\- you do not need a gateway per Availability Zone, as it is resilient by design.
	\- runs from the border of the VPC and the AWS Public Zone.
	\- allows services inside a VPC which are allocated with Public IPv4 addresses or IPv6 to be reached from the Internet and to connect to the AWS Public Zone or the internet. 
	\- managed gateway as AWS handles the performance.

* For IPv6, all addresses that AWS uses are natively, publicly routable.

**Internet Gateway VPC Diagram**

![Virtual Private Cloud (VPC) Basics-07-08-2024-9](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-9.png)

**IPv4 Addresses with a IGW (Request going from EC2 Instance to Linux Server Update)**

![Virtual Private Cloud (VPC) Basics-07-08-2024-10](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-10.png)

**IPv4 Addresses with a IGW (Request going from Linux Server Update to EC2 Instance)**

![Virtual Private Cloud (VPC) Basics-07-08-2024-11](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-11.png)

Bastion (Jump Box)
	\- an instance in a Public Subnet.
	\- they’re used to allow incoming management connection which then used to access internal-only VPC resources.
	\- management point or as an entry point for private-only VPCs.
	\- can be configured to only accept connections from certain IP addresses, to authenticate with SSH or to integrate with on-premises identity services.

## Stateful vs Stateless Firewalls

![Virtual Private Cloud (VPC) Basics-07-08-2024-12](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-12.png)

Transmission Control Protocol (TCP) (Layer 4)
	\- a connection based protocol.
	\- a connection is established between two devices using a random port on a client and a known port on the server.
	\- once established the connection is bi-directional.
	\- the connection is a reliable connection, provided via the segments encapsulated in IP Packets.
	\- each side is basically sending IP packets to each other.
	\- runs on top of IP.
	\- adds error correction together with the idea of ports.

**2 Parts of a Connection:**

1. Request
	\- where the client requests some information from a server.
	\- request component is always going to be (destination) to a well-known port.

2. Response
	\- where the data requested is returned to the client.
	\- always the inverse direction of the request.

![Virtual Private Cloud (VPC) Basics-07-08-2024-13](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-13.png)

**Connection Setup:**

1. Client picks a temporary or ephemeral port (Value between 1024 - 65535).
	- the range is dependent on the OS of the device the client is using.

2. Client initiates a connection to the server using a well-known destination port number.

Well-Known Port Number
	\- a port number which is typically associated with one specific popular application or protocol (e.g. TCP Port 443 = HTTPS).

1. Request a stream of data to the server to return a data whether it may be pictures or a webpage.

2. Server responds back with the actual data. The server responds using the source port of TCP/443 and the destination ephemeral port picked by the client (Response).

![Virtual Private Cloud (VPC) Basics-07-08-2024-14](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-14.png)

* Directionality of Inbound or Outbound depends on perspective (Client/Server).

![Virtual Private Cloud (VPC) Basics-07-08-2024-15](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-15.png)

Stateless Firewall
	\- it doesn’t understand the state of connections.
	\- it sees the request connection from a client to the server and the response as two individual parts.
	\- you need to allow or deny them as two parts or Two Rules (1 Inbound & 1 Outbound) per Connection.
	\- any rules you create for the response you will need to often allow the full range of ephemeral ports.

Stateful Firewall
	\- intelligent enough to identify the response for a given request.
	\- allowing the Request means the Response is automatically allowed as well.
	\- you’ll generally only have to allow the request or not, and the response will be allowed or not automatically.

## Network Access Control Lists (NACL)

* NACLs are stateless, which means they don’t know if traffic is Request or Response.
* Every subnet has an associated NACL and this filters data/traffic as it crosses the boundary of that subnet.
* Any data coming in (Inbound) and out (Outbound) the subnet is affected.
* Connections between things within that subnet are not impacted by NACLs.

![Virtual Private Cloud (VPC) Basics-07-08-2024-16](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-16.png)

**2 Sets of Rules in an NACL**

* Rules match the Destination IP or IP range, Destination port, and Protocol.
* They can explicitly Allow or explicitly Deny traffic.

* Rules are processed in order, lowest rule number first.
* Implicit Deny if nothing else matches.

1. Inbound Rules
	\- only affect data entering the subnet.

2. Outbound Rules
	\- affect data leaving the subnet.

**Sample Architecture**

![Virtual Private Cloud (VPC) Basics-07-08-2024-17](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-17.png)

![Virtual Private Cloud (VPC) Basics-07-08-2024-18](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-18.png)

* These rule-pairs (app port & ephemeral ports) are needed on each NACL for each communication type which occurs:

	1. Within a VPC
	2. To a VPC
	3. From a VPC

![Virtual Private Cloud (VPC) Basics-07-08-2024-19](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-19.png)

Default NACL
	\- created with the Default VPC which contains Inbound and Outbound rule sets that have the default implicit deny, but also a catch-all allow.
	\- the result is all traffic is allowed (Allow all by Default basically).

![Virtual Private Cloud (VPC) Basics-07-08-2024-20](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-20.png)

Custom NACL
	\- can be created for a specific VPC.
	\- initially does not have any subnet associations.
	\- only has 1 rule for both Inbound and Outbound sets which is the Default deny.

**Key Points:**

![Virtual Private Cloud (VPC) Basics-07-08-2024-21](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-21.png)

* NACLs are stateless as they view Request and Response as different things.
* A NACL only affects data that is crossing the subnet boundary.
* NACLs can Explicitly Allow and Deny.
* Uses only IPs, CIDR ranges, Ports and Protocols. No logical resources.
* Cannot be assigned to a logical resource (AWS resource), but only subnets.
* Each subnet can have one NACL (Default or Custom).
* A NACL can be associated with many subnets

Best Practice:

* Use security groups to allow traffic and NACLs to deny traffic.


## Security Groups (SG)

* Security Groups are Stateful because they detect response traffic automatically for a given request.
* Allowing an Inbound or Outbound Request means the response is automatically allowed.
* Does not have explicit deny.
* Deny by Default (Implicit Deny).
* Security Groups operate above NACLs on the OSI 7 layer stack which means they have more features.
* Supports IP/CIDR, Ports, Protocols, and logical resources (AWS resources including other SGs or itself).
* They are not attached to instances nor subnets. They attach to specific Elastic Network Interfaces (ENIs) or Primary Network Interface of that instance.

**Security Group Basics**

![Virtual Private Cloud (VPC) Basics-07-08-2024-22](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-22.png)

**Logical References**

![Virtual Private Cloud (VPC) Basics-07-08-2024-23](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-23.png)

![Virtual Private Cloud (VPC) Basics-07-08-2024-24](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-24.png)

**Self References**

![Virtual Private Cloud (VPC) Basics-07-08-2024-25](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-25.png)

## Network Address Translation (NAT) & NAT Gateway

* A set of different processes which can adjust IP packets by changing their source or destination IP addresses.
* Internet Gateway performs static NAT.
* NAT is many private IPs into one single Public IP.
* Gives Private CIDR range outgoing only access to the public internet and the AWS Public Zone.
* You cannot initiate connections from the Public Internet to these Private IP addresses when NAT is used.
* Not Free Tier eligible.

IP Masquerading 
	\- hiding a CIDR Blocks behind one IP.

**2 Ways AWS Provides NAT Gateway**

1. EC2 Instance configured to provide NAT.
2. Managed service which is the NAT Gateway.

**NAT Architecture**

![Virtual Private Cloud (VPC) Basics-07-08-2024-26](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-26.png)

* Let’s assume that Instance 1 is looking for software updates.
* Instance 1 send a request packet to the NAT Gateway.

Packet
	\- consists of the Source Address (Instance 1) and Destination Address (Software Update Server).

![Virtual Private Cloud (VPC) Basics-07-08-2024-27](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-27.png)

* Upon receiving the packet the NAT Gateway makes a record of the data packet, and modifies the packet.
* NAT records everything it needs into a Translation Table.
* NAT changes the packet’s Source IP Address (Instance 1) into its own IP Address (NAT Gateway IP Address).
* After changing the Source IP Address it then sends the packet to the Internet Gateway.

Translation Table
	\- a records of all of the packet information.

![Virtual Private Cloud (VPC) Basics-07-08-2024-28](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-28.png)

* The Internet Gateway then proceeds to modify the Source IP Address into the associated Public IP Address.

**Key Points:**

* NAT Gateways need to run from a Public Subnet because it needs to be able to be assigned a Public IPv4 address.
* A NAT Gateway uses a special type of Public IPv4 Address called an Elastic IP (Static IPv4 Public).
* NAT Gateways are a AZ Resilient Service.
* For Region Resilience, deploy a NAT Gateway in each AZ that you’re using in a VPC and then have a route table for private subnets in that AZ pointing at the NAT Gateway.
* They are a Managed Service.
* They can scale to 45 Gigabits per Second in Bandwidth.
* Partial hours are billed as a full hourly charge and also a charge based on the amount of data you process.

**NAT Gateway Full Resilience - VPC Design (A4L Sample)**

![Virtual Private Cloud (VPC) Basics-07-08-2024-29](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-29.png)

**NAT Gateway vs NAT Instance**

![Virtual Private Cloud (VPC) Basics-07-08-2024-30](images/Virtual%20Private%20Cloud%20(VPC)%20Basics-07-08-2024-30.png)

* NAT is not required for IPv6, but also does not work with IPv6.
* The main focus of NAT is to allow Private IPv4 addresses to be used to connect in an outgoing only way to the AWS Public Zone and Public Internet.
* Inside AWS, all IPv6 addresses are publicly routable.
* ::/0 Route + IGW for Bi-directional connectivity.

Egress-Only Internet Gateway
	\- outbound only gateway.
	\- you use it when you want to give an IPv6 instance outgoing only access to the internet to the AWS Public Zone and Public Internet.

