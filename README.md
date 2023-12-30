# SAA-C03
Study notes for the SAA
## Databases

# RDS
Relational database service is a managed,  **private** DAAS allowing you to create instance from widely-used database engines. 

**MultiAZ- Instance**

A single instance deployed in one AZ, a standby deployed in another, same-region. Reads and writes are done on the single primary instance, and updates are replicated synchronously to the standby. 

The standby is used for failover and can take up to 2 minutes to be used as the primary db 

Instances have their own dedicated EBS volume storage 

Backups are sent to S3 

**MultiAZ - Cluster** 

One primary instance where writes and reads occur, and two reader instances in different AZs, same region. Data is replicated synchronously across the read instances. 

Read instances are used in case of failover, and amazon will select the one with the most up-to-date data as the primary. Post-failover another read instance is created automatically. All instances still have their own dedicated storage. 

Backups/Snapshots are sent to s3 

There are 3 different endpoints that can be used for accessing the instances 
* Cluster endpoint - used for accessing the writer instances
* Read endpoint - "splits" requests between the two reader endpoints 
* Instance endpoint - access to once instance directly. This is good for dev instances! 

** Backup and Recovery **

# Aurora 

# Redshift

# DynamoDB
* Fully managed no-sql database; **public** database-as-a-service 
* Data is stored in tables that are replicated across 3 different AZs, one being the leader node 
* Tables have items which are identified by a unique, simple or composite primary key
**Pricing** 

**Backups** 
The two ways to backup data are as as follows: on-demand and PITR:
* on-demand works like rds snapshots, and the creation and deletion are managed by you 
* PITR is a stream of changes over a 35 day interval where you can restore any point in time into a new table 

**Capacity Modes and Units** 
There are two capacity modes: on-demand and provisioned 
* on-demand is more expensive, but requires less overhead because the capacity is adjusted automatically based on the load
* provisioned is much cheaper, but it takes planning to determine how much should be used when creating a table 
There are two units to keep in mind: read and write capacity units 
* 1 RCU = 4kb per second and 1 WCU = 1kb per second 

Operations 
* Query: Pull data from the table based on the simple or composite key. Remember that it costs one full RCU at least, so it's best practice to have one query pull as much data as needed 
* Scan: More flexible query option, but way more expensive due to the entire table being scanned regardless of the filter. This can be costly if the tables are large. Use indexing instead. 

**Consistency Modes:**
There are two consistency models you need to be aware of: eventual and strong 
* All write operations are always performed on the lead node, strong consistency always. 
* SC reads force the reads to always be done on the lead node. EC reads are performed on one of the replicated nodes, for half the price of SC. There is a small possibility that stale data could be pulled when performing EC reads, hence the price. 
* Global tables, which are tables that are replicated across-regions, use eventual read consistencyy

Indexing 
Indexing provides different views of a table, where the query is defined by something else, like attributes. There are local secondary indexes and global indexes: 
* LSI: 
	* The key does not change, but you can select data based on the attribute in addition to the key 
	* There's a limit of 5 LSIs per table
	* Must be defined at the table's creation
* GSI: 
	* You can select data based on a new key! 
	* Can be defined and modified after the table is created

Streams and Triggers 
