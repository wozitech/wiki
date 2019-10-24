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

