![AWS Logo](/uploads/logos/aws-logo.png "AWS Logo"){.pagelogo}
<!-- TITLE: AWS Facts and Figures -->
<!-- SUBTITLE: Collected during preparation for Associate Certifications -->

[Associate Solution Architect](solutionsArchitect)
[Associate Developer](developer)
# DynamoDB Throughput Calculations
* Reads:
	* 10 items of 6KB per second - eventual consistency = 10 * (2 blocks of 4KB) = 20 then divide 2 = 10.
	* 10 items of 1KB per second - eventual consistency = 10 * (1 block of 4KB) = 10 then divide by 2 = 5.
	* 15 items of 5KB per second - strong consistency = 10 * (2 blocks of 4KB) = 20.
	* 17 items of 10KB per second - eventual consistency = 17 * (3 blocks of 4KB)  = 51 then divide by 2 = 25.5 round up = 26.
* Writes:
	* 6 items of 4KB per second - 6*4 = 24.


# DynamoDB
* Optimistic Concurrent control
* Eventual Consistency Read, unless otherwise specified (strong consistency read)
* Primary Key, and additionally sort key (objects stored in order of sort key)
* Partition Key Max length: 2048 bytes. Sort key max length: 1024 bytes.
* Local secondary index - same primary key but different sort key
* Primary and Local indexes created only when table is created; global indexes created and deleted anytime
* Indexes are charged
* Streams - stored for 24 hours; used as input trigger to Lambda.
* Up to 35-deep nested documents.
* Up to 5 secondary and 5 global indexes per table
* DynamoDB Streams - data is stored for 24 hours in log.
* Up to 256 tables per region (increased on request)
* Item size up to 400KB.
* `ProvisionedThroughputExceededException`
	* Strong reads - 4KB per second
	* Consistent reads - 4KB per second / 2 (round up!)
	* Writes - 1KB per second
* `scanindexforward=false` reverses results using sort key (e.g. order by desc)
* Query defaults to eventually consistent; have to override for strongly consistent (e.g. DescribeTable)
* `AssumeRoleWithWebIdentity` - "Federation Mobile"/"Web Identity"
* This is pitched really to allow direct access to DynamoDB from mobile apps.
* Queries: `GetItem`,`DeleteItem`,`PutItem`,`Query`,`BatchGetItem`,`BatchWriteItem`
* Scans: `Scan`
* `BatchGetItem` - upto 16MB and 100 documents;UnprocessedKeys to get next batch
* `BatchWriteItem` - upto 16MB and 25 documents; puts and deletes, cannot update

# Redshift
* Starts with single node having 160GB of storage.
* Then scales with one leader node, and one or more compute nodes.
* Upto 128 compute nodes.
* Max 100 database per account (increase on request).
* Max 100 tables per database (increase on request).
* Max 20000 partitions per table (increase on request).
* Max 500 connections per cluster.
* All data is encrypted at rest; defauly is SSE (Amazon own) but KMS is also supported.
* One available zone only.
* Automated snapshots enabled by default; every 8 hours or 5GB of changed data. Default retention is 1 day (can be changed). All snapshots are deleted when cluster is deleted.Only Redshift can delete an automated snaphot. Set retention to 0 to delete all snapshots.
* Manual snapshots have to be deleted manually.
* Redshift includes same amount of S3 storage for snapshots as for data itself; charged if using more owing to large number of snapshots.
* Redshift automated snapshots can be copied to one other region (setup in configuration). Default retention period in copu region is 7 days.


# SQS
* 256KB text, billed in 64KB chunks
* Up to 10 messages in one request
* Standard queue does not guarantee once delivery or in sequence delivery
* Visibility timeout, defaults 30 seconds up to 12 hour maximum
	* `ChangeMessageVisibility`
* Retention - min 60 seconds, 4 days default to maximum 14 days
* Long poll - maximum 20 seconds; `ReceiveMessageWaitTimeSeconds` to non-zero.
* FIFO - 300 trans per second; FIFO queue names "MUST" have ".fifo" suffix.

# SNS
* Email, text, SQS or web service including Lambda
* Push direct to mobile devices
* Deliver once guarantee, but no guarantee on order.
* Email - plain (human) and JSON variants (machine); must confirm subscription by reply
* Customised messages for target subscription (_one message type for SMS another for email_)

# SWF
* Retention - 1 year measured in seconds
* Guaranteed delivery once and in sequence
* Workers - programs that interact with SWF to "do stuff"
* Decider - programs that control interaction re order, sequence and concurrency

# Elastic BeanStalk (EB)
* Platforms: Node.js,PHP,Python,Ruby,Tomcat,.NET,Java,Go,Packer(custom AMI), Docker: Glassfish, Go, Python
* No redundacny, multi-AZ or custom
* Load Balancing & Auto Scaling
* Application/Infrastructure - Rolling Updates/Immutable/Partial (e.g. test new releases)
* Recommend to provision RDS prior to BEanStalk and then elect into other RDS instance is deleted when EB is deleted.
* EB is free.

# S3
* Bucket name:
	* The bucket name can be between 3 and 63 characters long, and can contain only lower-case characters, numbers, periods, and dashes.
	* Each label in the bucket name must start with a lowercase letter or number.
	* The bucket name cannot contain underscores, end with a dash, have consecutive periods, or use dashes adjacent to periods.
	* The bucket name cannot be formatted as an IP address (198.51.100.24).
* Read after write on new objects
* Eventual read on updates/deletes
* Multipart upload  > 100MB
* Max PUT is 5GB file
* Min file size 0 byte on standard; **128KB on IA**
* Retention minmum 30 days on IA.
* Up to 100 buckets (more on request)
* Durability:
	* 99.9999999% Standard
	* 99.99% on IA
* Availability:
	* 99.99% - Standard
	* 99.9% - IA
* CORS policy
* `x-amz-server-side-encryption` header to apply server side encryption

# Glazier
* 90 days minimum retention (charged prorata for anything less)
* 32KB metadata in Glacier and 8KB metadata in S3 for ref object
* Retrieval:
	* Expedited: up to 5 minutes
	* Standard: 3-5 hours
	* Bulk: 5-12 hours

# Snowball
* Using 1Gb link - up to 70TB in 7 days.
* Not practical for less than 10TB.
* 80TB model available in all regions.

# SMS (Server Migration Service)
* 50 concurrent migrations

# CloudWatch
* Retention - 2 weeks
* Default - 5 minutes
* Min - 1 minute
* CPU, disk, network and status (system/instance) are by default.
* RAM is not - requiring custom metric and some code.
* Default: every 5 minute polling. Up to 1 minute frequency.
* Alarms sent to queue or email (SQS/SNS).
* Emails must be acknowledge before used; up to 72 hours to confirm email.
* Actions, such as restart/terminate instance.
* Events, such as run lamdba function to add EC2 to ELB once started

# EC2/EBS
* Uptime/availability: 99.95%
* Mim/Max Sizing:
	* General Purpose (SDD): 1GB (min) to 16TB (max)
	* Provisioned IO (SSD): 4GB (min) to 16TB (max)
	* Cold HDD/Throughput Optimised: 500GB (min) to 16TB (max)
* I/Os:
	* Standard SSD: 3000 IOP burst on standard - at 1TB; 3 IOPs per GiB
	* Provisioned: upto 20000
	* Throughput: 500
	* Cold: 250
* Throughput (HDD based on 1MB blocksize; SSD on 16KB block size):
	* General Purpose: 160MB/s
	* Provisioned IO: 320MB/s
	* Throughput optimised: 500MB/s
	* Cold: 250MB/s
* Root partition - 10GB max for instance store, 1TB or 2TB for SSD
* Up to 40 attached EBS volumes (not a hard limit)
* Reserved Instance (RI) categories:
	* Standard - maximum discount - fixed for duration
	* Convertible - less discount, but can be changed only if it results in a high value RI (you pay more)
	* Scheduled - to launch within a given time window (e.g. overnight batch processing)

# EFS (Elastic File System)
* NFSv4
* Read after write consistency

# Route 53
* Routing Policies:
	* Simple - single resource
	* Weighted - by comparison of value, e.g. 50/50, 1/2/3. 99/99/33
	* Latency - AWS returns the lowest latency to end user
	* Failover - primary & failover with healthcheck
	* Geolocation - based on country of origin of end user
* Healthchecks - all, except simple, support healthchecks and mapped to:
	* IP/DNS of target
	* Other Route 53 healthchecks
	* CloudWatch alarm
* Alias:
	* ELB
	* BeanStalk
	* S3 bucket (as static web site only)
	* CloudFront
	* Route 53 hosted zone records

# CloudFormation
* Upto 200 stacks by default (can request more)
* No limit to number of templates
* Is free to use.
* Resolves all dependencies, e.g. create VPC before subnets before EC2 before deploying app
* JSON and YAML supported
* AWS Config to track changes
* Templates can be stored in S3, and have embedded templates.
* Automatic rollback on error
* `WaitCondition` - to wait for applications to be deployed (how?)

# Lambda
* node.js, java, python. C# (recently)
* Priced duration and #requests
* Duration priced on #GB (RAM) per second
* First 1million requests are free; then $0.20 per million.
* Triggers: CloudWatch alarm or S3 putobject or SNS/SQS trigger, CodeBuild, Kenesis, IoT, Alexa Skillset or via the AWS API Gateway.
* Each function is autonomous - can't interact with others except through queue or events

# RDS
* 6TB maximum storage (for provisioned IOPs) and a maximum of 30000 IOPs.
* 1 or 3 year contract with reserved pricing.
* All up front, partial up front and no up front discounting (greatest to least).
* Read Replicas: 5 max for MySQL, MariaDB and postgres, 15 max for Aurora
	* MariaDB/MySQL only - read replica can be a different region
* Automated backup - Retention: 1 day (min) 7 days (default) up to 35 days. 0 disables backups
* Read replica; async replication. Multi-AZ; sync replication.
* Automated backups must be enabled for read-replicas.

# VPC
* `/16` max and `/28` smallest - VPC and subnet
* subnet cannot span AV.
* AWS reserves 5 IPs within a subnet:
	* network - e.g. x.x.x.0
	* broadcast - e.g. x.x.x.255
	* gateway - e.g. x.x.x.1
	* Reserved for future use (except DNS server below) - e.g. x.x.x.2
	* Reserved for future use - e.g. x.x.x.3
* A VPC has a single DNS server running on the second IP of the VPC CIDR, e.g. x.x.x.2.
* Up to 5 VPCs per region and upto 200 subnets per region (increased on request).
* Never delete the default VPC!
* VPC peering can be within and between AWS accounts; e.g. for cooperation between two customer's VPCs (IP address clashes though).
* Default VPC includes one default subnet for each region within VPC. Default ACL is to allow all in and out, and all subnets are public (with a route to the Internet).
* NAT Instance - disable source/destination check.
* Network ACLs are applied in a sequence order. Security group applies ALL rules.
* Must associate subnet with a Network ACL; one one Network ACL per subnet. Multiple subnets can use the same Network ACL.
* Don't forget that a VPC needs an IGW (Internet GW) and the Route Table for the subnet must have a default route 0.0.0.0/0 to the IGW.


# STS (Security Token Service)
* Identity Broker (takes identity from A and joins it to B); have create own Identity Brokers.
* Identity Store - e.g. AD, Facebook, Google.
* Identity - User.
* STS Federation approach:
	* Develop an Identity Broker to communicate with LDAP/AD and AWS. Can be by user or by role (LDAP group).
	* The Identity Broker always authenticate with LDAP/AD first, then AWS STS to access a named policy.
	* Application then gets temporary access to AWS resources
* Three Identity Brokers provided by AWS - using "claim based authentication":
	* Federated - AssumeRoleWithSAML - SAML, as used with AD/LDAP
	* Federated Mobile - AssumeRoleWithWebIdentity - openID, as used with Facebook, Google, github, Amazon (preferred for own apps).
	* Accounts - other AWS accounts
* AWS Cognito is a managed "Identity Broker" service facilitating federated login with OpenID, oAuth2 and SAML "Identity Stores".

# AWS CLI
* `ec2 describe-instances`
* `ec2 describe-images (--owner self) --filter ""`
* `ec2 run-instances`
* `ec2 start-instances` - only starts existing instances
* `ec2 terminate-instances`
* `--dryrun`
# AWS SDK
* browser (restful!), NodeJS (Javascript), C++, PHP, Java, .NET, Android, python, ruby and Go
# MetaData
* curl http://169.254.169.254/latest/meta-data/
* Not a localhost loookup. Trottled by AWS.

# Kenesis
* 1 shard alows 5 reads per second up to 2MB per second
* 1 shared allows 1000 records per second write up to 1MB (very fast writes)
* 