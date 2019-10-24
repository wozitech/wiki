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
* 