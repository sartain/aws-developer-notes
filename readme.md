# AWS notes

## The big picture

### Core Services

#### Elastic Cloud Compute (EC2)

##### Background

<ul>
<li>Instance is a virtual server, operating system agnostic.
<li>Instances increase and decrease based on usage.
</ul>
##### Starting an Instance
<ol>
<li>Select AMI (Amazon machine image) which is Operating System and Software used.
Amazon updates the image software to patch security vulnerabilities etc but once launched is not responsible.
Amazon marketplace provides additional AMI's.
<li>Select Instance Type e.g. Compute optimized or Storage optimized family.
<li>Select Instance Type size e.g. small, medium, large.
<li>Select number of instances to replicate, same image and type.
Creates an auto-scaling group which increases or decreases the number of instances based on the usage.
<li>Select storage type e.g. EBS (Elastic block storage) and size
<li>Option of setting tags/labels. Very useful for shared groups e.g. project members
<li>Configure Security Group, controlling IPs that instance talk to/from.
Example, only enable SSH sessions from current IP address. This would be expected in production.
Set inbound and outbound rules on the instance.
<li>In configuring security group, also open port that application will be running on. Set port in port range when creating a new Rule to accomplish this.
<li>Set a key pair, enabling SSH within the instance.
Can create new or use existing.
</ol>

#### Simple Storage Service (S3)

File storage service.
Used for any file type, maximum file size = 5TB.
Maximum upload in one PUT = 5GB.
Bucket = root resource to perform CRUD operations.
S3 Buckets can:

<ul>
<li>Trigger events when objects are added, modified or deleted.
<li>Preserve older versions of objects.
<li>Replicate objects across regions.
</ul>

Buckets are assigned URL's to access the objects within them.
Objects have permissions, enabling S3 to host static files for websites for example.
There is an option in configuration to enable this.
CloudFront (CDN) content delivery network enables caching with edge locations to store the static files.

#### Relational Database Service (RDS)

Partly managed by AWS doing:

<ul>
<li>Scheduled automatic backups
<li>Simple software updates
<li>Managed infrastructure
</ul>

Different database options such as MySql or Amazon Aurora.
Different EC2 options to run the database on e.g. memory optimized or storage optimized.
Benefits over EC2 database:

<ul>
<li>Take DB Snapshots
<li>Change the hardware e.g. increase database size
</ul>

Security:

<ul>
<li>Use security groups like EC2
<li>E.g. only give database access to your application
<li>Or give users read access to some data for viewing purposes.
</ul>

For non-relational, DynamoDB is available or MongoDB for documents.

#### Route 53

DNS servicing inside and outside of AWS.
Useful tool for routing to an S3 or EC2 application.
Easiest way to enable interaction between AWS services.
Setup:

<ol>
<li>Setup a hosted zone (root domain name) e.g. google.com
<li>Setup subdomains if necessary e.g. example.com and route to AWS resources.
</ol>

Route 53 also provides health checks and can determine if a web application is returning 404 response.

#### Elastic Beanstalk

Adds convenience to make AWS easier to run with.
Provides deployment benefits like:

<ul>
<li>Easy deployment with various tools
<li>Set it and forget it configuration
<li>Aggregated monitoring and logging
</ul>

Enables deploying an application to a test or production environment.
Environment = rules and configuration managing EC2 instances.
Can setup load balancing etc at Elastic Beanstalk.
Monitoring provides information like:

<ul>
<li>Number of requests
<li>CPU Utilization
<li>Network traffic
</ul>

#### Lambda

Lambda's provide the ability to write serverless code and run it without spinning up a server.
As simple as writing a python file e.g. `lambda_function.py`
Only pay for what you use, e.g. code running on several EC2 instances and then stops.
No need to manage servers, they are increased and decreased based on requirement when executing the code.
Significant cost savings for infrequent activity.
Structure:

<ol>
<li>Code to execute
<li>Platform e.g. Python or Node
<li>Triggers e.g. API gateway
<li>Configuration e.g execution timeout, memory requirements or IAM role required.
</ol>

#### Dynamo DB

NoSQL storage of data.
Benefits:

<ul>
<li>Unlimited, elastic storage
<li>No hardware choices
<li>Pay only for what you use
</ul>

Data is stored in a table as database is abstracted.
DynamoDB provides a **provisioned throughput capacity** which is outlined below:
`provisioned throughput capacity = Number of read/write units per second`
The provisioned throughput capacity is used to correctly size the database.
Even with a small provisioned throughput capacity, there is still extremely fast performance.

#### VPC (Virtual Private Cloud)

Provision components in a secure location, making it easy to secure a group of components e.g. EC2 instances and Dynamo DB's.
Whilst security groups secure individual instances, a VPC secures a group of instances.
Inside a VPC there are subnets in which resources can be grouped further.
Each subnet can have different access rules.
Multiple subnets are often used for public and private subnets to restrict some resources more than others.
How to control access to resources?

<ul>
<li>Routing Table - Control what goes where (e.g route outbound traffic to a NAT gateway to mask the IP).
<li>ACL (Access control list) - Subnet level firewalls (e.g. prevent IP ranges from accessing information)
</ul>

Basic configuration for a VPC is free, and helps to provide multiple levels of security.

#### CloudWatch

A monitoring service which can alert or alarm based on metrics for Amazon services.
Can give a notification or autoscale the EC2 instances.
Logs can be used to trigger notifications e.g. a certain error occurs a set number of times.
Dashboards can be setup to view the metrics.

#### CloudFront

Content Delivery network which caches data at edge locations.
Reduces latency and enables global access to content.
How to setup:

<ul>
<li>Create a cloudfront distribution
<li>Each distribution defines a set of content to be served e.g. images
<li>Specify original location e.g. S3 bucket
<li>Unique cloudfront URL assigned to the distribution, this URL will be used to access content.
<li>On a distribution configuration includes: Allowed HTTP methods, Edge locations and SSl certificates.
<li>Pricing based on start and edge locations
</ul>

### Harnessing the power of AWS from Command line to code

#### Software Development Kit

Code which makes it easy to interact with Amazon web services.
Each is open source and hosted on AWS github.
Nothing special, wraps HTTP requests to AWS services making it easier to use.
Examples:

<ul>
<li>Modify cloudwatch rules
<li>Invalidate Cloudfront distribution (remove file from cache)
<li>Read/Write to RDS
</ul>

#### Command Line Interface

Interact with AWS resources from the command line.
Useful for shell scripts.
Commands look like:
`aws <service> <command> <arguments>`

Easy installation, then adding access keys as an initial step.
Useful for:

<ul>
<li>Quick running of tasks with little overhead
<li>Automation tasks
<li>Shell scripts
</ul>

## AWS Developer getting started

### Welcome to AWS

#### Differences

Main difference is that AWS has single responsibility rather than one large application.
Many different services are used in an entire application.
Database becomes a service rather than port in same application.
Each service in AWS has a local IP address which is used to communicate with other services.
Communication often done via TCP.
Resources also have an ID which can be used in place of IP.

#### Pizza example

Application Hosting - EC2
Images and Assets - S3 storage
Users - Dynamo DB
Sessions - ElastiCache
Toppings - DynamoDB
Pizza (Combination of toppings) - RDS
Image (final pizza image) - S3

### Services

#### Cloudwatch

Set alarms and notifications for event response.
Example alarm use cases

<ul>
<li>DynamoDB read/write throughput
<li>EC2 CPU Usage
<li>Estimated Billing Charges
</ul>
This can help avoid 
<ul>
<li>Database failures, data access may be too slow for demand.
<li>EC2 systems reaching capacity, stopping an application being served.
<li>A large bill, above a specified budget.
</ul>

CloudWatch also provides actions in relation to events.
Consider the EC2 CPU Usage reaching too high a capacity, auto-scaling can ensure another instance is launched.

#### Simple Notification Service

Companion to CloudWatch, alerts can be pushed to endpoints.
Works as below:

<ol>
<li>Topic is created in region 1, enabling AWS events to be pushed
<li>SMS or Email consume the topic
<li>Notification pushed to a topic by cloudwatch event in region 1
<li>Topic forwards notification to endpoints (SMS, Email)
</ol>

#### Creating a Billing Alarm

<ol>
<li>Create a SNS topic e.g. billing
<li>Create a subscriber to the topic, e.g. email 
<li>Toggle 'Receive billing alerts' to 'On' in Billing Preferences
<li>Select metrics for billing
<li>Select Conditions - Amount before threshold is reached
<li>Alarm state trigger - Set reason for alarm trigger e.g. Outside Threshold or Insufficient Data
<li>Set SNS topic, e.g. billing
<li>If required, set actions on EC2 instances [not important for billing]
<li>Name alarm with description for future reference
<li>Create after viewing preview
</ol>

#### IAM

Access Management control
Usages:

<ul>
<li>Passwords
<li>Multi-factor authentication
<li>Access keys
<li>SSH keys
</ul>

A policy is often used to control access, resource or type level.
A policy can be attached to specific users or groups (e.g. developers).
Multi-Factor authentication can be enforced to improve security.

##### Policies

By default a user has zero policies.
A policy is a set of rules to enable access.
A policy statement has 3 properties to give or restrict access:

<ul>
<li>Effect: 'Allow' or 'Deny'
<li>Action: Operation user can perform based on service
<li>Resource: Specific resources a user can perform action(s) on
</ul>

There are standard AWS policies but user's can setup bespoke policies too.
Example custom policy = Give a user access to a specific resource only.

##### Groups

Amazon policies are suggested to be applied to groups instead of individual users.
Users can be in multiple groups.
Users can be added in the IAM dashboard, it is suggested in AWS to have a separate account to the root user.
A password can be assigned or manually created for a new user, and a setting enables creating a new one upon login.
The user will be given a URL to login to AWS, rather than traditional links.
Creating a group:

<ol>
<li>Name group
<li>Add initial user(s) to the group
<li>Attach permission policies (AWS managed or Custom)
<li>Suggestion = Follow least privilege
<li>Useful Amazon group = Power User (full access to services but not user and group management)
</ol>

#### EC2 and VPC

##### VPC

Used to isolate services from other AWS services and the outside world.
Connections can be made between VPC's within AWS as they have IP addresses assigned.
VPC's have a Security Group to define incoming and outgoing IP addresses. Acts as a mini-firewall.
Routing Table: Defines outgoing IP address, helps to create a proxy.
Network ACL: Defines incoming and outgoing IP address, which is allowed.
All instances are launched in a subnet, inside a VPC.
Subnet is isolated and has a CIDR block, routing table, ACL.
Common to have a private and public subnet.
Creating a VPC:

<ol>
<li>Select relevant region
<li>Select type e.g single public subnet
<li>Name VPC and select CIDR block (IP range etc)
<li>Name public subnet and select CIDR block (subset of VPC CIDR block)
<li>Create VPC
<li>Routing table will be offline and provides no way to connect to internet by default
<li>Navigate to Routing table and Routes tab
<li>Add route for 0.0.0.0/0 for anywhere
<li>Set target as 'igw' for internet gateway and select option available
<li>This enables outbound traffic to the internet
<li>Create more subnets as one subnet is one availability zone
<li>Add a new public subnet to cover another availability zone in the region via subnet section
<li>Ensure the CIDR block range is different to previous subnet
<li>Most secure way is to setup a NAT gateway to connect to the internet
<li>Another option is to auto-assign public IP addresses to subnets which is much cheaper
</ol>

##### EC2

A virtual machine provisioned with some CPU, storage etc.
AMI's can involve your own software being installed to the EC2 instance.
EBS = Elastic Block Store. Enables storage of instances to live independently to EC2 instance itself.
EBS enables termination of an instance and rebooting to not lose data.
Launching instance: [Starting instance](#starting-an-instance)

##### Connecting to EC2 Instance

Initially, there will be no public endpoint to connect to the EC2 instance from outside the VPC.
Elastic IP will need to be created, an elastic IP is a public IP address that is created, assigned and destroyed independently.
The Elastic IP can be associated with an instance, and once one instance is deleted can be associated with another:

<ol>
<li>Create a new Elastic IP address
<li>Associate with an EC2 instance
<li>Use Elastic IP address to communicate to EC2 instance
</ol>

Connect via SSH like below:

```
//Save the SSH access keys
chmod 400 ~/Downloads/pizza-keys.pem
//Connect to instance using SSH key file and Elastic IP
ssh -i <pem-file> ec2-user@<ec2-ip>
```

##### Updating and Deploying to an instance

`sudo yum update` common command to update the instance.
Once connected to EC2 instance, can install data e.g. Node.js and npm
SCP can be used to transfer files to the EC2 instance, such as an application
Command to transfer:

```
scp -r -i <pem_file> <local_code_to_transfer> ec2-user@<ec2_ip>:home/ec2-user
```

The local code can then be navigated to, actions such as 'npm install' and 'npm start' can be used to download and run the code.
The elastic ip can be used to access the running web app in a browser e.g. `<ec2_elastic_ip>:3000`

##### Scaling EC2 instances

With custom AMI's an EC2 instance can be saved as a snapshot and replicated.
Snapshots can be used in place of a linux instance and can launch the instance + npm dependencies for example.
Auto-Scaling group is preferred as it avoids creating new instances often.
A launch template + scaling rules are used to expand and decrease a pool of instances based upon load.
A load balancer is used to manage incoming requests to an instance and direct the request to an active instance in the pool.

##### Creating a Snapshot

<ol>
<li>Navigate to the EC2 instance you want to replicate
<li>Actions > Image and templates > Create image
<li>Modify size or other parameters if necessary
<li>Create AMI, this takes several minutes before being usable
<li>Launch a new instance and select 'My AMIs' to view the snapshot created
</ol>

##### Creating a Load Balance

There are 3 types:

<ul>
<li>Application: Web applications for HTTP and HTTPS
<li>Network: TCP and UDP, possibly for video streaming
<li>Classic: Older type and less relevant
</ul>

Creating a load balancer for an application:

<ol>
<li>Name load balancer
<li>Select if internet facing or not
<li>Select which ports to listen on (80 for HTTP, 443 for HTTPS)
<li>Select the VPC for the load balancer
<li>Select 2 availability zones, same as the subnets in the VPC
<li>Use existing or create new security group
<li>Configure routing, enable routing to an Instance
<li>Select protocol and internet facing port for the instance (e.g 3000)
<li>Configure health check to ensure instance is up, example is the root homepage of a web app
<li>Register targets - select which instances to register from the list
<li>The list is based upon the port and protocol entered in the previous page
</ol>

Need to manage sessions, ensure user connects to the same instance after logging in.
This is complete by enabling stickiness on the load balancer via the 'Target Group' header.
Enter the 'attributes' section and select 'Stickiness'.

##### Creating an Auto-Scaling Group
