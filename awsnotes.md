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
<li>Select storage type e.g. EBS (Elastic block storage)
<li>Option of setting tags/labels
<li>Configure Security Group, controlling IPs that instance talk to/from.
Example, only enable SSH sessions from current IP address.
Set inbound and outbound rules on the instance.
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
