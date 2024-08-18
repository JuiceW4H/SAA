**IAM Identity Policies**

* A type of policy that gets attached to IAM identities (IAM Users, IAM Groups, and IAM Roles).
* It grants or denies access to AWS products and features to any identity which uses that policy.
* Identity Policies or Policies documents are created using JSON.
* At a high-level a policy document is just one or more statements.

**Components of a Policy Statement:**
* A statement only applies if the interaction you are having with AWS match the action and the resource.

1. Statement ID (Sid) (Optional Field)
	\- it lets you identify a statement and what it does.
	\- it acts like a description basically.
	\- best practice is to always fill SID whether it is a big or small policy.

2. Actions (Action)
	\- it matches one or more actions.

Format: <\Service>:<Action/Operation>

3 Actions:

* A specific individual action
* A wild card or asterisk ‘*’ (All actions available for that service)
* A list of multiple independent actions

3. Resources (Resource)
	\- it matches AWS resources.
	\- specify an individual resource, or a list of resources.
	\- use ARNs when listing individual or a list of resources.
	\- can also use asterisks or “wild cards” to list all resources.

4. Effect
	\- its either Allow or Deny.
	\- it controls what AWS does when that “Action” and “Resource” part of the statement match the operation you’re attempting to do with AWS.

![IAM, Accounts and AWS Organizations-06-24-2024](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024.png)

**Priorities in IAM Policies**

![IAM, Accounts and AWS Organizations-06-24-2024-1](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-1.png)

* Deny Always Wins

1. Explicit Denies
	\- this automatically overrules all other statements.

2. Explicit Allows
	\- this takes effect unless there is also an explicit deny which is overlapped by the explicit deny.

3. Default/Implicit Denies
	\- AWS Identities start off with no access to any resources.
	\- If they’re not allowed access, they have no access.

**2 Main Types of Policies**

![IAM, Accounts and AWS Organizations-06-24-2024-2](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-2.png)

1. Inline Policies
	\- these are individually assigned per identity.
	\- best not to use if individuals can be grouped.
	\- use only for special or exceptional access rights (allows or denies) outside the normal managed policies.

2. Managed Policies
	\- these are essentially a policy that you can easily assign to multiple identities.
	\- reusable
	\- low management overhead
	\- can be modified and it will be automatically applied to all identities the policy is attached to.

## IAM Users and ARNs

![IAM, Accounts and AWS Organizations-06-24-2024-3](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-3.png)

![IAM, Accounts and AWS Organizations-06-24-2024-4](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-4.png)

IAM Users
	\- are an identity used for anything requiring long-term AWS access (e.g. Humans, Applications, or service accounts).
	\- if you can picture one thing, a named thing 99% of the time, the correct identity to select is an IAM user.
	\- Maximum of 5000 IAM Users per account.
	\- An IAM user can be a member of 10 groups. 

![IAM, Accounts and AWS Organizations-06-24-2024-5](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-5.png)

Principal
\- represents an entity trying to access an AWS account.
\- it makes requests to IAM to interact with resources.

Authenticated Identity
\- a principal that has undergone authentication.

Authentication
\- how it proves to IAM that it is the identity it claims to be.

Authentication
\- IAM checking the policy statements that apply to that identity and allowing or denying access to resources.

Authentication Methods:

* Username and Passwords
* Access Keys (CLI access)

Amazon Resource Name (ARN)
\- uniquely identify resources within any AWS account.
\- allows identification of a single resources.

ARN Format:

arn:partition:service:region:account-id:resource-id 
arn:partition:service:region:account-id:resource-type/resource-id 
arn:partition:service:region:account-id:resource-type:resource-id

![IAM, Accounts and AWS Organizations-06-24-2024-6](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-6.png)

## IAM Groups

![IAM, Accounts and AWS Organizations-06-24-2024-7](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-7.png)

* IAM Groups are basically containers for IAM Users.
* They exist to make organizing large sets of IAM Users much easier.
* You cannot login to a group as it does not credentials.
* There are no built-in all-users group in AWS IAM by default.
* No nesting in groups.
* A Soft Limit of 300 groups per account and a Hard Limit of 500 groups per account.

**2 Benefits of Using Groups:**

1. They allow effective administration-style management of users as they can represent teams or projects.
2. Groups can actually have policies (inline or managed policies) attached to them.

Resource Policies
	\- it controls access to a specific resource and it allows or denies identities in accessing that resource by referencing an identity/identities.
	\- uses ARNs to reference identities
	\- so a policy on a resource can reference IAM Users and IAM Roles using ARN.
	\- it cannot grant access to an IAM Group.
	\- you cannot reference an IAM Group from resource policies.

* Groups are not a true identity.
* They can’t be referenced as a principal in a policy.

## IAM Roles

* A role is best suited to be used by an unknown number or multiple principals.
* Possible use of multiple AWS users inside the same AWS account, or it could be humans, applications, or services inside or outside of the AWS account.
* Generally used on a temporary basis.
* A role represents a level of access inside an AWS account.
* Identities assume the role for a short period of time, they become that role, use the permissions of that role, and then they stop being that role.

![IAM, Accounts and AWS Organizations-06-24-2024-8](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-8.png)

2 Types of Policies for IAM Roles

1. Trust Policy
	\- this controls which identities can assume that role.
	\- it can references different things like identities in the same AWS account (IAM Users, IAM Roles, and AWS Services), and also identities in other AWS accounts.
	\- it can also allow anonymous usage of roles, and other types of identities (Facebook, Twitter, and Google).
	\- if a role gets assumed by something which is allowed to assume it, then AWS generates temporary security credentials which is made available to the identity that assumed the role.

Temporary Credentials
	- are like Access Keys, except instead of being long term (Access Keys) they are time-limited.
	- once they expire the identity will need to renew them by reassuming the role which then generates new temporary credentials.
	- they generate by the AWS Secure Token Service (STS)

2. Permissions Policy
	\- specifies what resources or permissions the role has with regards to interacting with the AWS services in that AWS account.

Permissions Boundary
	\- act as a restrictive filter on the permissions that can be granted to a user or role.

## When to use IAM Roles

* Provisioning of roles to AWS services that execute on the your behalf. (e.g. Lambda Function that terminates EC2 instances)

![IAM, Accounts and AWS Organizations-06-24-2024-9](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-9.png)

* Emergency use cases. 

![IAM, Accounts and AWS Organizations-06-24-2024-10](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-10.png)

* Implementing SSO from different Identity Providers or >5000 Identities needed.

![IAM, Accounts and AWS Organizations-06-24-2024-11](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-11.png)

* Mobile Application use case.

![IAM, Accounts and AWS Organizations-06-24-2024-12](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-12.png)

## Service-linked roles

* IAM role linked to a specific AWS service.
* They provide a set of permissions which is predefined by a service.
* Provides permissions that a service needs in order to interact with other AWS services on your behalf.
* Services might create/delete the service-linked role/s by itself.
* Service might also allow you to create the role during setup of that service or within the IAM.
* Difference between a normal IAM Role and a Service-linked Role is that you cannot delete the Service-linked Role until it’s no longer required/used. 

Reference: https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html

![IAM, Accounts and AWS Organizations-06-24-2024-13](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-13.png)

Role Separation
	\- the concept of giving one group the ability to create roles and another group to use them.

![IAM, Accounts and AWS Organizations-06-24-2024-14](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-14.png)

PassRole Permissions
	\- a method in AWS which gives you the ability to implement role separation.
	\- it is essentially a permission to that grants the ability for a user or a service to pass a pre-existing role into a another user or service that will then assume that role.

## AWS Organizations

* A product which allows large businesses to manage multiple AWS accounts in a cost-effective way with little to no management overhead.

Standard AWS Account
	\- an AWS account which is not within a organization.
	\- when you create the AWS Organization you are not creating it in the account you are just using the account to create an organization.
	\- the AWS account used to create an organization with becomes the “Management Account” (previously called Master Account) for the Organization.

Management Account (Master Account)
	\- using this account you can invite other existing Standard AWS Accounts into the organization.
	\- an organization can have only one Management or Master Account

Member Account
	\- upon approval/acceptance of the invite from the Management Account or Master Account the Standard AWS Account becomes a Member Account.
	\- an organization can have 0 or more Member Accounts. 

Structures in an AWS Organization

- It is also possible to create a structure of account within an organization which is useful if a business has multiple AWS accounts and you need to group them.
- Structures in an organization is hierarchical.

**Organizational Structure**

![IAM, Accounts and AWS Organizations-06-24-2024-15](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-15.png)

* It is possible to build a nested or multiple levels of organizational structure in Organizations 

* Organizational Root
	\- at the top of the hierarchy is just the root container of the organization.
	\- contains AWS accounts which are the Management or Master account, and the Member accounts.
	\- can also contain other containers which are called Organizational Units or OUs.

* Organizational Units
	\- can also contain the Management or Master account, and  Member accounts.
	\- it is simply a subdirectory concept.

![IAM, Accounts and AWS Organizations-06-24-2024-16](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-16.png)

* Consolidated billing allows the removal of the individual billing methods are removed from the Member accounts which is passed on to the Management or Master account (Payer Account)
* One bill for all the billable usage of all the accounts within the AWS Organizations.
* Consolidation of reservations and volume discounts.

Service Control Policies (SCP)
	\- gives the ability to restrict what the AWS accounts within the organization can do.

* It is also possible to create new AWS account/s directly within the organization all that is needed is a valid unique email address.
* By creating an AWS account within the organization this by passes the invite step in the common process.

![IAM, Accounts and AWS Organizations-06-24-2024-17](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-17.png)

Best Practices: 

* Reduces the need for IAM Users in every single AWS account as IAM Roles can instead be used to allow IAM Users to access other AWS accounts.
* Have a separate account dedicated to handle logins or identities.
* You can use the internal AWS IAM or Identity Federation to use on-premises identities to access the designated login account.
* User Role Switch feature to switch to other Member accounts of the organization which basically assumes roles in these other AWS account (Member account).
* Name the IAM Role for the Role Switch to “OrganizationAccountAccessRole” if you are to create the role manually.

## Service Control Policies (SCP)

* A Policy Document or a JSON document
* These can be attached to an individual AWS account, Organization as a whole or to one or more Organizational Units.
* The effects of the SCP is cascading or inherited (e.g. an OU containing 2 more OUs and 3 Member accounts has an SCP. The effects of that SCP is passed also into the Member Accounts inside the 2 OUs and also among the 3 Member accounts).
* Management or Master account cannot be affected by SCPs.
* SCPs are account permissions boundaries
* They limit what the account can do (even the root user indirectly).
* They don’t grant any permissions, but are just a boundary.
* They define what is and isn’t allowed within the account.
* SCPs will limit the permissions that can be assigned to individual identities.

Best Practice:

* Avoid using the Management or Master account in any AWS service due to its immunity from the SCPs. 
* SCP also defaults to an implicit deny if there is no initial allow for Full Access.

**2 Ways of using an SCP:**

* Deny List (Default) 
	\- allow by default and block access to certain services.
	\- when SCPs are enabled in an organization AWS applies to the Organization and all OUs a default policy which is called Full AWS Access.
	\- by Default nothing yet is restricted rendering SCPs to no effect.

![IAM, Accounts and AWS Organizations-06-24-2024-18](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-18.png)

* Allow List
	\- block by default and allow access to certain services.
	\- remove the Full AWS Access policy therefore rendering the implicit deny in place instead then add services that you would allow permission access to.

![IAM, Accounts and AWS Organizations-06-24-2024-19](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-19.png)

## CloudWatch Logs

* It is a public service meaning it can be usable on AWS VPCs, other cloud platforms, or an on-premise environments assuming you have network connectivity and AWS permissions.
* The endpoints which applications to connect to is hosted in the AWS Public Zone.
* Allows you to Store, Monitor, and Access logging data.
* It has built-in integrations with other AWS services.
* Capable of taking logging data and generating metrics based from it (Metric Filter).
* It is a regional service.

![IAM, Accounts and AWS Organizations-06-24-2024-20](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-20.png)

Logging Data
	\- a piece of information data, and a timestamp.

CloudWatch Agent
	\- for anything outside AWS or for logging custom service/application metrics you can use the Unified CloudWatch Agent.

Log Events
	\- contains a timestamp and a message block.
	\- these come from the sources (Compute Services, Mobile Applications, Databases, External APIs and etc) that inject data into CloudWatch Logs.

Log Streams 
	\- a sequence of log events from a specific source for a specific thing.
	\- where log events are stored.

Log Groups
	\- containers for multiple log streams for the same type of logging data.
	\- also stores configuration settings.
	\- where we define retention settings and permissions which applies to all log streams in that log group.
	\- where metric filters are also defined.

Metric Filters
	\- constantly reviewing any log events for any log streams in that log group looking for certain patterns which upon detection increments the metric.

## CloudTrail 

* It logs API calls/activities as a CloudTrail Event.
* To customize the service create 1 or more Trails.
* CloudTrail is a regional service. 

![IAM, Accounts and AWS Organizations-06-24-2024-21](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-21.png)

CloudTrail Event
	\- a record of an activity in an AWS account (actions taken by a user, role, or service).

 
CloudTrail Event History
	\- CloudTrail by default stores the last 90 days of CloudTrail events in the CloudTrail event history.
	\- a feature that is enabled by default (no cost for 90 days of history).

**3 Different Type of CloudTrail Events:**

* By default CloudTrail only logs Management Events because Data Events are often much higher volume.

1. Management Events (Enabled by Default)
	\- provide information about management operations that are performed on resources in your AWS account.
	\- also known as control plane operations (e.g. creating or deleting an EC2 instance).

2. Data Events (Not Enabled by Default)
	\- contain information about resource operations performed on or in a resource (e.g. objects being uploaded to an S3 or accessed from an S3). 
	\- can be enabled/explicitly set  when a trail is created.

3. Insight Events

CloudTrail Trail
	\- the unit of configuration within the CloudTrail product.
	\- a way you provide configuration to CloudTrail on how to operate.
	\- a trail logs events for the AWS region it’s created in. 

![IAM, Accounts and AWS Organizations-06-24-2024-22](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-22.png)

**2 Configurations of a Trail:**

1. One Region (single-region trail)
	\- it is only ever in the region it is created in.
	\- it only logs events for that region.

 
2. All Regions (all-region trail)
	\- a collection of trails in every AWS region, but its managed as one logical trail.
	\- if AWS adds new regions, all-region trail is automatically updated.

* Most services log events in the region where the event occurred or the region that resource is created in.
* There are a small number of services that log events globally into one region (e.g. IAM, STS, or CloudFront they always log events to US-East-1 which is N.V.).

Global Service Events 
	\- services that log events globally into one region mainly US-East-1 or Northern Virginia.
	\- trail needs to have this option enabled in order to log global service events.
	\- this feature is enabled by default if you create a trail inside the user interface (AWS Console). 

* A trail by default stores events in a definable S3 bucket.
* You are only charged for the storage used in S3.
* Logs are stored as compressed JSON log files. 
* It can also be integrated with CloudWatch Logs and the data can be stored in CloudWatch logs.
* You can create an AWS Organization trail this means if you create this trail from the management account you it can store all of the information for all of the accounts inside that organization.

**Important Elements of CloudTrail:**

![IAM, Accounts and AWS Organizations-06-24-2024-23](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-23.png)

* CloudTrail is enabled by default on AWS accounts, but its only the 90 day event history that’s enabled by default so you don’t get storage in S3.
* Trails are how you take the data that CloudTrail has access to and store it in better places such as S3 and CloudWatch logs.
* The default for trails is to store Management Events only which only includes Management Plane Events.
* Data events need to be specifically enabled which comes at an extra cost.
* IAM, STS, and CloudFront log their data as Global Service Events which gets logged in US East 1 (Northern Virginia) and a trail will need to be enabled in order to capture that data.
* CloudTrail is not real-time logging.

Pricing Reference:

* CloudWatch: https://aws.amazon.com/cloudwatch/pricing/

* CloudTrail: https://aws.amazon.com/cloudtrail/pricing/

## AWS Control Tower 101

* To allow the quick and easy setup of multi-account environments.
* Orchestrates other AWS services to provide the functionality it does, and one of those services is AWS Organizations.
* Control Tower uses Organizations, IAM Identity Center, CloudFormation, Config, and more.
* Think of Control Tower as another evolution of AWS Organizations adding more features, intelligence, and automation.
* It provides via other AWS services SSO and ID Federation, Centralized Logging and Auditing.

![IAM, Accounts and AWS Organizations-06-24-2024-24](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-24.png)

Landing Zone
	\- this is the multi-account environment part of the Control Tower.
	\- a feature designed to allow anyone to implement a well-architected multi-account environment.
	\- built using AWS Organizations, AWS Config, CloudFormation, and much more.

![IAM, Accounts and AWS Organizations-06-24-2024-25](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-25.png)

Home Region
	\- the region that you initially deploy a product into.
	\- you can explicitly allow or deny the usage of other AWS regions, but the home region, the one you deploy into, is always available.

Guardrails
	\- detect or mandate rules and standards across all AWS accounts in the Landing Zone.
	\- these detect drifts away from governance standards, or prevents those drifts occurring in the first place.
	\- rules for multi-account governance.
	\- Guardrails can be enforced or not enabled.
	\- Guardrails statuses are Clear, In-Violation, and Not Enabled

 
**3 Types of Guardrails:**
* Mandatory
	\- always applied

* Strongly Recommended
	\- recommended by AWS

* Elective (Optional)
	\- can be used to implement fairly niche requirements.

**Functions of Guardrails:**

* Preventative
	\- stop you doing things within your AWS accounts in your Landing Zone.
	\- implemented using SCPs.
	\- stops things from occurring.

* Detective
	\- more so of a compliance check.
	\-  uses AWS Config rules and allows you to check that the configuration of a given thing within an AWS account matches what you define as best practice.
	\- only identities things and compares to your defined standards.**

![IAM, Accounts and AWS Organizations-06-24-2024-26](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-26.png)

Account Factory
	\- automates and standardizes account creation, modification, or deletion.
	\- can be interacted with through the Control Tower console or via service catalog.
	\- creates AWS accounts in a fully automated way as many as the business needs.
	\- configuration of these accounts is handled by Account Factory.
	\- Guardrails defined are automatically applied to these automatically provisioned AWS accounts.
	\- accounts are configured with standard account and network configuration.
	\- allows accounts to be closed or repurposed.

* You can allow any member of your organization within tightly controlled parameters to be able to provision accounts for any purpose which you define as okay and that person will be given admin rights over that AWS accounts provisioned.

Control Tower Architecture 
![IAM, Accounts and AWS Organizations-06-24-2024-27](images/IAM,%20Accounts%20and%20AWS%20Organizations-06-24-2024-27.png)

Dashboard
	\- a single page oversight of the entire environment. 

Management Account
	\- same concept with the AWS Organizations.
	\- account that creates the Control Tower becomes the Management Account for the Landing Zone.

* When control tower is first setup it generally creates 2 Organizational Units (OU), the Foundational Organizational Unit (Security) and Custom Organizational Unit (Sandbox).
* Inside the Foundational or Security OU AWS Control Tower creates 2 AWS accounts which are the Audit Account and Log Archive Account.

**Accounts found in the Security OU:**

Log Archive Account
	\- for users that need access to all logging information for all of your enrolled accounts within the Landing Zone.  
	\- you need to explicitly grant access to this account, and it offers a secure read-only Archive account for logging. 

Audit Account
	\- for users that need access to the audit information made available by Control Tower.
	\- you can also use this account as a location for third-party tools to perform auditing of your environment.

Custom or Sandbox Organizational Unit
	\- generally used for testing and less rigid security situations.

* You can create other Organizational Units or AWS Accounts.
* Utilized IAM Identity Center (formerly AWS SSO) for SSO, multiple-accounts, and ID Federation. 
* Provides monitoring and notifications using CloudWatch and SNS.
* End users can create new AWS accounts using the Service Catalog.

ID Federation
	\- you can use your existing identity stores to access all these different AWS accounts.

