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

#### Policies

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

#### Groups

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

### EC2 and VPC

#### VPC

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

#### EC2

A virtual machine provisioned with some CPU, storage etc.
AMI's can involve your own software being installed to the EC2 instance.
EBS = Elastic Block Store. Enables storage of instances to live independently to EC2 instance itself.
EBS enables termination of an instance and rebooting to not lose data.
Launching instance: [Starting instance](#starting-an-instance)

#### Connecting to EC2 Instance

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

#### Updating and Deploying to an instance

`sudo yum update` common command to update the instance.
Once connected to EC2 instance, can install data e.g. Node.js and npm
SCP can be used to transfer files to the EC2 instance, such as an application
Command to transfer:

```
scp -r -i <pem_file> <local_code_to_transfer> ec2-user@<ec2_ip>:home/ec2-user
```

The local code can then be navigated to, actions such as 'npm install' and 'npm start' can be used to download and run the code.
The elastic ip can be used to access the running web app in a browser e.g. `<ec2_elastic_ip>:3000`

#### Scaling EC2 instances

With custom AMI's an EC2 instance can be saved as a snapshot and replicated.
Snapshots can be used in place of a linux instance and can launch the instance + npm dependencies for example.
Auto-Scaling group is preferred as it avoids creating new instances often.
A launch template + scaling rules are used to expand and decrease a pool of instances based upon load.
A load balancer is used to manage incoming requests to an instance and direct the request to an active instance in the pool.

#### Creating a Snapshot

<ol>
<li>Navigate to the EC2 instance you want to replicate
<li>Actions > Image and templates > Create image
<li>Modify size or other parameters if necessary
<li>Create AMI, this takes several minutes before being usable
<li>Launch a new instance and select 'My AMIs' to view the snapshot created
</ol>

#### Creating a Load Balance

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

#### Creating an Auto-Scaling Group

Involves setting up a launch template and then the auto-scaling group.
Creating launch template:

<ol>
<li>Select name for launch template
<li>Select AMI, ideally a snapshot of an instance
<li>Select instance type used already, or other if improving or decreasing
<li>Option to select SSH keys, not used in production but useful for troubleshooting in test
<li>Select VPC with security group already created
<li>In advanced details -> User data: we can write scripts
<li>For example: npm start. To start the application
</ol>

Create auto-scaling group:

<ol>
<li>Select name and launch template created previously
<li>Select VPC being used for instance in network section
<li>Select available subnets in VPC to balance load across both
<li>Select load balancer created previously
<li>Set desired, minimum and maximum capacity's
<li>Setup Scaling Policy to scale based on metrics
<li>An example metric is the network traffic with a target value that can be tweaked in the future based on demand
<li>Create auto-scaling group
<li>To access the load balancer in browser -> Load Balancers -> Specific load balancer -> DNS name
<li>Aim would be to setup a domain name and route any requests to the DNS name of the load balancer to route traffic
<li>For security we can edit 'inbound rules' for instances to ensure they only accept traffic from the load balancer
<li>Setup the security group of the load balancer in the 'Source' option
</ol>

#### Testing requests

Open in browser, deleting cache and re-entering
JMeter or Apache Benchmark to simulate load balancing and trigger scaling
We can use activity history in AWS to view the scaling in practice

### S3

Store objects (file and metadata) in S3.
Metadata includes information such as file type and modification date.
Object key is the `path/filename.filetype`
e.g. `images/image.png`
To access data, S3 or IAM policies can be setup to control access.
Otherwise, access can be given to specific resources e.g. web resources available to anyone.
S3 offers cross-region replication, data added or modified is replicated in another region to reduce latency.
May be useful if main users are in USA and UK.
If users are in more than 2 locations, a Content Delivery Network like CloudFront should be used instead.

#### Buckets

Buckets are used in S3 to store the objects.
Each bucket has a URL to access objects with.
Creating a bucket:

<ol>
<li>Name the bucket uniquely + select region
<li>Select public access, private by default
<li>Create bucket
<li>Modify bucket permissions, can also modify object permissions
<li>Setup a bucket policy to enable users to have permission to view objects
<li>Use Policy Generator to do this
    <ol>
    <li>Select policy type
    <li>Select Principal (who has access)
    <li>Select Service e.g. S3
    <li>Select Action e.g. view files
    <li>Select ARN e.g. bucket name
    <li>Add more statements or generate policy
    </ol>
<li>If public, AWS will have warnings to tell user
</ol>

#### Uploading Files to buckets

Can use: Console, CLI and SDK.
CLI useful for bulk upload.
Command:

```
aws s3 cp <local_folder> s3://<bucket>/<remote_folder> --recursive --exclude "<pattern>"
```

With commands `--recursive` copying everything in the directory and `--exclude "<pattern>"` to avoid copying over files.
E.g.

```
aws s3 cp ./assets/js s3://pizza-luvrs-a-unique-string-alex/js --recursive --exclude "./DS_Store"
```

#### Retrieving files from S3 via code

Useful to retrieve URL, can do this by grabbing the URL of an object and removing the object key.
To modify the CSS of a local directory to AWS follow steps below.
Original:

```html
<html>
  <head>
    <link rel="stylesheet" href="/assets/css/stylesheet.css" />
  </head>
</html>
```

AWS:

```html
<html>
  <head>
    <link
      rel="stylesheet"
      href="//pizza-luvrs-a-unique-string-alex.s3.us-east-2.amazonaws.com/css/stylesheet.css"
    />
  </head>
</html>
```

#### Uploading files to S3 via code

Add the SDK to Javascript with the components needed

```JS
const {
    PutObjectCommand,
    S3Client
} = require('@aws-sdk/client-s3')
```

To upload add code like below with example of uploading an image:

```JS
const params = {
    Bucket: 'bucket_name',
    Key: 'object/objectname.objecttype',
    Body: Buffer.from(data, 'base64'),
    ContentEncoding: 'base64',
    ContentType: 'image/png'
}
const client = new S3Client({ region: 'us-east-2' })
const command = new PutObjectCommand(params)
await client.send(command)
```

To retrieve the file URL:

```JS
return `//pizza-luvrs-a-unique-string-alex.s3.us-east-2.amazonaws.com/${params.Key}`
```

#### CORS (Cross Origin Resource Sharing)

Need to enable as URL may be different for resources e.g. one folder to another in S3.
To do this:

<ol>
<li>Navigate to buckets
<li>Navigate to permissions
<li>Navigate to CORS section
<li>Add a JSON document to enable access
</ol>

#### Accessing S3 with EC2

Need to assign credentials with EC2 to enable access to S3.
An example is at the 'Launch Template' adding IAM roles:

<ol>
<li>Add a role (not a user, something to attach policies to)
<li>When creating the role, select the service to use (e.g. EC2)
<li>Attach a policy related to use case, focus on Least-Privilege
<li>Name and create the role
</ol>

Modify launch template

<ol>
<li>Navigate to existing template -> Actions -> Modify
<li>Advanced Details -> IAM Instance Profile
<li>Select the IAM Role like the one created above
<li>Ensure auto-scaling group is using most-recent or latest launch template to enable the new launch template to be used
<li>Another option (if 'default' on auto-scaling group) is to modify launch template to 'Default' version
</ol>

### Databases

Benefits for databases include:

<ul>
<li>Improved scalability, serve many customers without using large amounts of memory
<li>Persistence, avoid losing information after sessions
</ul>

#### RDS

Relational databases which run on EC2 instances and have Amazon responsible for configuration etc.
Amazon help do:

<ul>
<li>Software upgrades
<li>Nightly database backups, configurable and can be stored from 1-35 days
<li>Ability to restore a database from a backup
<li>Monitoring
<li>Simple creation and configuration options
<li>Availability - Multi Availability zones can be used to replicate the data
<li>Automatic failover, if one availability zone fails the other can be used
</ul>

##### Read Replica

A non-production copy of the database available to run queries and analyse the data without impacting production.
There is eventual consistency with the source but not used as a fail safe.

#### Choosing an RDS Option

Can consider which database is used locally e.g. SQL, there are options in AWS to migrate to Aurora with lower costs.

#### Creating an Instance

<ol>
<li>Select standard or easy create, easy removes some options
<li>Select engine e.g Amazon Aurora or PostgreSQL and version
<li>Select sample template e.g. Production or Test
<li>Set name for database instance
<li>Set master login & password to access the database
<li>Set storage, with option of storage being increase up to a threshold
<li>Set connectivity e.g. VPC to place database
<li>Set public access (No for production)
<li>Set a VPC Security Group for the database to control access
<li>Ensure security group enables communication to the database
<li>Add a database in the Additional Configuration section
<li>Create database
</ol>

#### Connecting to a database

For PostgreSQL via pgAdmin:

<ol>
<li>Add new server to create a new connection
<li>Grab 'Endpoint' from 'Endpoint and Security' section after navigation to relevant database
<li>Paste value in 'Host' section of 'Connection' tab
<li>Enter username and password setup when creating the DB
<li>
</ol>

#### Interacting with RDS

Connect to the database as you would normally, using the AWS URL and credentials to access.
Common to use a ORM library to convert data to objects and avoid writing SQL code to remove risk of vulnerabilites.
`Sequelize` can be used for JS.

#### Dynamo DB

NoSQL solution for databases in AWS.
Main structure:

<ul>
<li>Table
<li>Items within a table
<li>Primary Key (same for each item in a table)
</ul>

The provisioned throughput capacity, reads and writes for 4kb files, can be modified at any time.
Additional features:

<ul>
<li>On-demand capacity mode. Table capacity scales as needed, priced per table or index request [expensive]
<li>Provisioned capacity auto-scaling mode. Increase or decrease capacity based on rules (like auto-balancer)
</ul>

Both options enable to option of using On-Demand and as you understand database size, ability to change.

#### Relational or not?

RDS provides structured schemas for storing data.
Dynamo DB is much more flexible with how the data looks.
New fields can be added any time in Dynamo DB, speeding up development time and iteration.
RDS is much better for querying data and filtering based on values.
Dynamo DB is useful if only using a primary key to retrieve values, using other properties is difficult.

#### Creating a table in DynamoDB

<ol>
<li>Set table name
<li>Set Partition [Primary] key and type
<li>Use default settings or custom
<li>
</ol>

#### Connecting to DynamoDB table

The AWS SDK is required to connect to the dynamoDB table.
Example of loading SDK:

```Node
const { DynamoDBClient } = require("@aws-sdk/client-dynamodb")
```

The client helps to marshall and unmarshall data into JSON.

```JavaScript
const marshallOptions = {
    convertClassInstanceToMap = true
}
```

Add SDK values for updating the DB:

```JavaScript
const {
  DynamoDBDocumentClient,
  GetCommand,
  PutCommand,
  ScanCommand,
} = require("@aws-sdk-lib-dynamodb")
```

Setup client:

```JavaScript
const client = new DynamoDBClient({region: 'us-east-2'})
DynamoDBDocumentClient.from(client, { marshallOptions })
```

Sending a PUT:

```JavaScript
const params = {
    TableName: "table_name",
    Item: "item"
}
const client = //same as above
const command = new PutCommand(params)
await client.send(command)
```

Retrieving all items:
This is often paginated as there is a 1MB limit, the example has no pagination.

```JavaScript
const params = {
    TableName: "table_name"
}
const client = //same as above
const command = new ScanCommand(params)
const response = await client.send(command)
return response.Items
```

Retrieving a specific item:
The idKey is the key value name, with idValue being the specific value.

```JavaScript
const params = {
    TableName: "table_name",
    Key: {
        [idKey]: idValue
    }
}
const client = //same as above
const command = new GetCommand(params)
const response = await client.send(command)
return response.Items
```

#### Running on EC2

To run data on the EC2, permission access needs to be set by adding policies on the EC2 instance.
For an RDS instance, the RDS instance needs to be added to the security group too.

### Elastic Beanstalk and CloudFormation

#### Cloud Formation

A service in AWS to provision resources based on templates.
Example would be having development and production stacks separately.
CloudFormation reduces error risk and time spent.
A CloudFormation template is a JSON document containing the configuration for AWS resources.
Version control is useful for this.

#### Cloud Formation Stack

A stack can be created, collection of Instances and resources to be created.
Updating a stack, e.g. increasing size of EC2 instance, can be done without restarting resources if possible.
Stack can be deleted if no longer needed (deleting all resources).

#### Provisioning Resources

Resources can be provisioned in a template, large YAML document.
There is a resource section with information representing different resources to be provisioned.
Each resource can have properties which can be updated.
Different parts of the document can be referenced using:

```yaml
!GetAtt MyLaunchTemplate.DefaultVersionNumber
!Ref MyLaunchTemplate
```

#### Creating a Stack

<ol>
<li>Use pre-created template or from scratch
<li>Upload template file if possible (CloudFormer can form a stack from existing application)
<li>Name Stack
<li>Specify stack details - EC2 AMI ID and KeyPair for EC2 instances
<li>Configure Stack options if required
<li>Create Stack
</ol>

A stack can be deleted by navigating to the stack and selecting 'Delete'.

#### Elastic Beanstalk

Automate the deployment of an application.
Provides: Scaling, Monitoring, Resource Provisioning by uploading application code.
Elastic Beanstalk is dedicated to running the application - provisioning resources and running the application.
Components:

<ol>
<li>A logical application e.g. Web App
<li>A single platform e.g. Node or Java
<li>One+ Application version (versions of code)
</ol>

Main usages are to have production and development environments.
Can deploy an application version to production after it has been tested.

#### Deploying an Application with Elastic Beanstalk

<ol>
<li>Elastic Beanstalk - Create application
<li>Name application
<li>Select platform e.g. Node and version
<li>Use sample code or upload code (eg offline application)
<li>Create application
<li>Navigate to Network and set the correct VPC
<li>Navigate to Instances and set security groups for the EC2 instances
<li>Navigate to Security and select key-pair with IAM role
<li>Navigate to Capacity and select environment type as 'Load-Balanced'
<li>Navigate to Load Balancer and add the info for load balancing e.g. ports as before
<li>In reality, a new stack on CloudFront is created and launched
</ol>

#### Configuring Permissions for Elastic Beanstalk

Add RSD and DynamoDB policies to EC2 IAM role, enabling the databases to be used.
To do this, follow below:

<ol>
<li>Navigate to IAM -> Roles -> Specified EC2 role
<li>Navigate to Permissions
<li>Attach a Policy related to RDS and DynamoDB
<li>Attach a Policy related to Elastic Beanstalk (AWSElasticBeanstalkWebTier) to push logs etc
<li>Navigate to RDS and select database instance
<li>Navigate to Security Group and select relevant group
<li>Modify inbound rules to give access to internal IP (EC2 security group) with database as type
</ol>

To restart an instance to apply changes like this, Elastic Beanstalk can be used.
Navigate to application - Actions - Restart.

### CloudFront and ElastiCache

Aims to reduce latency, especially if data centres are located away from users.

#### CloudFront

The request moves to CloudFront, where the closest 'edge' location serves the content.
A set of content edged with CloudFront is called a 'distribution'.
A distribution provides content from 1+ origins like S3 buckets.
There are fine controls over caching behaviour e.g. time to live for content.

#### Creating a CloudFront distribution

<ol>
<li>Set origin for the distribution e.g. Elastic Load Balancer
<li>Set any other configurations
<li>Modify default Cache behaviour
<li>An example is to redirect HTTP to HTTPS
<li>Can also allow additional HTTP methods like GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE
<li>Option to set a caching policy with information like time to live
<li>Caching policy can contin information on Query Strings and Cookies
<li>In Settings - Price class - Select edge locations to use
</ol>

#### Configuring a CloudFront distribution

There is an option to add additional origins. This would enable multiple applications to use the same cache.
Additional behaviours can be added for different path patterns e.g. /pizza or /login.
An example is :

<ol>
<li>Setup a new behaviour
<li>Enter the path pattern e.g. pizza/*
<li>Set origin group e.g. load balancer
<li>Redirect HTTP to HTTPS for more security
<li>Set 'GET, HEAD' as only HTTP methods to prevent caching 'PUT' calls
<li>Set an additional Cache policy to increase the time to live, useful for content like images which are unlikely to change
<li>Ability to view metrics on the CloudFront cache to understand and monitor
<li>
</ol>

#### ElastiCache Overview

In-memory cache data stores are used (e.g. Redis, Memchaced) to serve content quickly.
Very similar to RDS in instrumentation, it is a managed service for in-memory cache datastore.
Provides:

<ul>
<li>Managed maintenance, upgrades etc.
<li>Automatic read replicas
<li>Simple node management and scalability
</ul>

ElastiCache structure involves a collection of nodes running a single cache instance.
Memcache clusters have 1-20 nodes, a node is an individual EC2 instance running the caching software.
Redis structure involves a cluster with 1 node and up to 6 read replicas.
This enables additional scalability with Redis which is the preferred option.

#### Configuring a Redis structure in ElastiCache

<ol>
<li>Add a security group to accept inbound port (6379 in example) with EC2 as the source security group
<li>Create ElastiCache cluster -> Redis engine
<li>Name cluster and select port (same as used for security group)
<li>Can enable replication in the production settings
<li>Select node type (EC2 instance)
<li>In advanced, create cache subnet group
<li>Enables VPC to host ElastiCache clusters
<li>
</ol>

#### ElastiCache in Code

In the start server section:

```JavaScript
async function startServer () {
  const server = Hapi.Server({
    port: 3000,
    cache: [
      {
        name: 'redis',
        provider: {
          constructor: require('@hapi/catbox-redis')
          options: {
            partition: 'cache'
            host: 'primary endpoint from elasticache in AWS without colon and port e.g. pizza-cluster.awsz6d.0001.use2.cache.amazonaws.com'
          }
        }
      }
    ]
  })
}
```

In the plugins.js Setup Cache

```JavaScript
const cache = server.cache({
  cache: 'redis', //Same name as cache in startserver above
  segment: 'sessions',
  expiresIn: 24 * 60 * 60 * 1000
})
```

#### Giving App permission to use ElastiCache

Modify IAM role and attach an 'elasticache' policy.

#### Cleaning up AWS resources

Delete:

<ul>
<li>ElastiCache: Cluster, Backups, Subnet Group
<li>ElasticBeanstalk: Application [also deletes environment and resources]
<li>DynamoDB: Tables
<li>RDS: Database Instances without taking final snapshot or backups
<li>S3: Empty Bucket, then delete
<li>EC2: Load Balancer and any target groups, Auto Scaling Group, Launch Template Instances, Elastic IP address, AMIs, Keypairs, EBS Snapshots, Security Groups [may need to delete inbound rules first]
<li>VPC: VPC
</ul>

---

## Small Case Study Examples

### FC Barcelona

FC Barcelona running websites, mobile apps, social media.
Setup:

<ul>
<li>Route 53 for DNS name resolution
<li>CloudFront for content delivery network (media and assets)
<li>S3 for scalable assets and media documents storage
<li>EC2 with Elastic Load Balancing and Auto Scaling for: Dynamic generated HTML and XML pages
<li>Cloudwatch for alarm and monitoring
<li>Simple Notification Service for platform notifications
<li>Relational Database Service for database (mySQL)
<li>CloudFormation for automation and provisioning
</ul>

CloudFormation is of benefit for one-click deployment of an entire infrastructure.
Elastic Cloud solution handles peaks, e.g. football team during matches.
Supports a large amount of content (6,000 pages and 12,000 photographs).
Other case studies enable services to be added or removed easily.

---

## Designing and Developing with AWS

### Intro

#### Service Domains in AWS

Services are grouped into domains e.g. storage and security.
Compute: Runs code that you provide e.g. EC2, Lambda, Elastic Beanstalk, Batch.
Storage: S3, EFS, Storage Gateway, Glacier.
Database: RDS, DynamoDB, ElastiCache, DocumentDB.
Networking and CDN: VPC, CloudFront, API Gateway, Route 53
DevTools: CodeStar, CodeCommit, CodeBuild, CodeDeploy, CodePipeeline, X-Ray
Management: CloudWatch, CloudFormation, CloudTrail
Security: IAM, Certificate Manager, Key manager
Analytics: Redshift, EMR, OpenSearch, Kinesis
Application Integration: SNS, SQS, SWF, Step Functions

#### Exam Prep

Requires:

<ul>
<li>Knowledge of AWS services
<li>Options and details of each service
<li>Different problems and scenarios when using each service
</ul>

Contains:

<ul>
<li>Multiple Choice Questions
<li>Service specific topic, detail or limit
<li>Debugging and architecture
<li>Likely: EC2, S3, DynamoDB
</ul>

Preparation:

<ul>
<li>Example projects in AWS
<li>Working with AWS services and being familiar with how they work
<li>Complete the example project given the notes above and gain familiarity, referring to course content if required.
</ul>

Budget:

<ol>
<li>Ensure an alert is set when a charge is reached
<li>AWS Budgets can use this
<li>Select Cost Budget
<li>Billed monthly so use monthly code, can set recurring too
<li>Set a fixed budget with an amount small enough to detect something has been left on
<li>Can set tags in budgeting with 'budgeting scope'
<li>Name the budget and configure an alert with a threshold, allows warning before reaching.
</ol>

### Launching Instances
