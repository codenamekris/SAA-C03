# SAA-C03
Study notes for the SAA
# RDS

#database
## Overview

Relational database service is a managed,  **private** DAAS allowing you to create instance from widely-used database engines. 
* All RDS instance are launched within a VPC and need their own subnet group
## Provisioning an Instance 

## Deployment Types

**Single AZ** 
* A single instance in a single AZ, not standby available 
* Backups are done on this single instance, which impacts performance, since the database is down until the process is finished

**MultiAZ w/ Standby**
Improved durability and availability
* An instance is deployed in one AZ, with a synchronous standby in another. Both have their own dedicated EBS volumes for storage...
* Only the primary instance can be read from and written to, and it is accessed through an endpoint
* In case of failover, the database CNAME points to the standby and it takes 60 -120 seconds for this to be completed
	* The other database becomes the standby once it's available again
	* [There are other reasons for failure to consider](https://aws.amazon.com/blogs/database/amazon-rds-under-the-hood-multi-az/)
* Snapshots and backups are taken from the standby with no impact on performance of the primary

**MultiAZ w/ Readable Standbys (cluster)  
Improved  
* A writer instance with up to two readable, synchronous standbys
* Data is considered committed when data is written to the primary and only one of these standbys
* There are [three endpoint connection options](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/multi-az-db-clusters-concepts-connection-management.html): 
	* Cluster endpoint - points to the writer instance 
	* Reader endpoint - load balances between the two readers
	* Instance endpoint - connects to a specific endpoint in the cluster, each instance has its own

One primary instance where writes and reads occur, and two reader instances in different AZs, same region. Data is replicated synchronously across the read instances. 

Read instances are used in case of failover, and amazon will select the one with the most up-to-date data as the primary. Post-failover another read instance is created automatically. All instances still have their own dedicated storage. 

There are 3 different endpoints that can be used for accessing the instances 
* Cluster endpoint - used for accessing the writer instances
* Read endpoint - "splits" requests between the two reader endpoints 
* Instance endpoint - access to once instance directly. This is good for dev instances! 

## **Read Replicas
* Can be in the same region or cross-regional, meaning you can achieve global resilience 
* Asynchronous replication 
* They have their own endpoint for access
* Their can be a max of 5 different replicas each providing an additional instance of read performance 
* Data is synched from the main DB so there is nr 0 RPO - do not use in case of data corruption 
* Can be promoted quickly - low RTO 

## **Backup and Recovery**  
[RPO and RTO Overview](https://aws.amazon.com/blogs/mt/establishing-rpo-and-rto-targets-for-cloud-applications/) 
[AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)

These improve RPOs, but RTO is not the best since it takes a long time  

**Manual Snapshots 
* A snapshot is a full cop of the data stored on an instance, and then incremental changes thereafter
* Snapshots are stored in S3 buckets managed by AWS, so it's not something you can see on your end 
* The instance is unavailable when snapshots are being taken, so it's best to do so during downtime
* Snapshots persist even after the instance is no longer available 
* Restore from a snapshot can take a long time 

**Automated Backups 
* Backups occur once a day at a time chosen by AWS or you
* Backups are created from the transaction logs written to S3 every 5 minutes 
* The retention period for backups is 0 to 35 days 
* Restoration is instant, but there's a 5 minute lag in data 
## **Cost

## Security 

[Read about data encryption]( https://dev.to/documatic/data-encryption-securing-data-at-rest-and-in-transit-with-encryption-technologies-1lc2)
