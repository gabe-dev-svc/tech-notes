# CSA Exam - Databases on AWS

## Summary 
- RDS ()OLTP)
- DynamoDB (No SQL)
- Red Shift (OLAP)
- Elasticache
  - Memcached
  - Redis
- RDS runs on VMs, cannot SSH into these machines, though.
- RDS is patched by Amazon
- Aurora Serverless IS Serverless, the rest of RDS is not
- DynamoDB is serverless
- Two types of backups for RDS
  - Automated
  - Snapshots
- Read replicas
  - Can be Multi-AZ
  - Used to increase performance
  - Must have backups turned on
  - Can be in different regions
  - Can be MySQL, PostgreSQL, MariaDB, Oracle, Aurora
  - Can be promoted to master, but will break read replica
- MultiAZ in RDS
  - Used for data recovery
- DynamoDB stored on SSD Storage
- Spread across 3 geographically distict data
- Eventual consistenct and strongly consistent reads available
- Red Shift used for business intelligence, only available in one AZ right now
- Red shift backups enabled by default w/ 1 day retention period, max retention period is 35 days
- Aurora will always have 2 copies of data contained in each AZ with minimum of 3 AZ
- Aurora snapshots can be shared with other AWS accounts
- Aurora has automated backups turned on by default
- Elasticache 
- Use Redis if looking for Multi-AZ or need backups and restores
- Use Memcached if you need to scale horizontally

- RDS
- DynamoDB
- Elasticache
- Redshift 

RDS Two Key Features:
  - Multi-AZ for disaster recovery
    - Connection string points to primary database, if fails, Amazon updates DNS address with new IP address. Implemented failover.
  - Read replicas for performance
    - Every write is written to a (read) replica. If something fails, there's no automatic failover. 
    - There can be 5 copies of read replicas

Non Relational Databases
- Does not require consitencies across records

Data Warehousing
  - Used for business intelligence 
  - Used to pull in very large and coplex data sets
  - OLTP vs OLAP
  - Amazon Redshift: Amazon's Datawarehousing solution

ElastiCache - In-memory cache service 
  - Memcached
  - Redis

Backups
  - Automated Backups allow recovery of database to any point in time within a retention period
    - Point in time recovery 
    - Enabled by default
    - Backups taken within a defined window
    - Not stored after deleting RDS instance
  - Snapshots are taken manually/user-initiated. Stored even after deleting RDS instance
  - Restores are restored to brand new RDS instances

Encryption
- Done using AWS KMS service
  - Replicas and snapshots are also encrypted of encryption is enabled 

What is Multi AZ?
- Exact copy of production database available in another AZ
- Amazon RDS automatically fails over 
- Available for 
  - SQL Server
  - Oracle
  - MySQL
  - PostgreSQL
  - MaraiDB

What is a read replica?
  - Production database asynchronously replicates to read replicas
  - Can be architected to have reads be read from read replicas and writes to a single replica that then cascades
  - Allows to have reas-only copy of production database. 
  - Use read replicas primarily for very read-heavy database workloads. 
  - Available for 
    - SQL Server
    - Oracle
    - MySQL
    - PostgreSQL
    - MariaDB
  - Automatic backups must be turned on in order to deploy a read replica. 
  - Each read recplica will have its own DNS endpoint
  - Read repicas can be multi AZ
  - You can have a read replica in a second region 

DynamoDB
  - Supports both document and key-value data models
  - Spread across 3 geographically distinct data centers
  - Stored on SSD storage
  - Eventual constency and strongly consistency
    - Eventual: Consistency across all copies of data found within 1 second of modification/insertion
    - Strongly: Consistency across all copies of data found less than 1 second of modification/insertion

Redshift
  - AWS Datawarehousing solution
  - Used for business intelligence
  - $0.25/hour, $1000/Tb/year
  - Configuration
    - Single Node (160Gb)
    - Leader Node/Compute Note
      - Leader: Manages client connections/receives queries
      - Compute notes: Store data, perform queries/computations
  - Advanced compresssion:
    - Redshift doesn't require indexes or materialized views
    - Automatically selects best compression scheme
  - Parallel processing
  - Backups enabled by default, 1 day retention period, maximum retention period is 35 days
  - At least three copies of data (original, replica on compute notes, and backup on S3)
  - Priced on compute node hours, 1 unit per node per hour. Leader node hours not charged
  - Charged for backup and data transfer within VPC
  - Encrypted in transit using SSL, at rest using AES-256, default RedShift takes care of key management. 
  - Currently onlt available in 1 AZ
  - Can restore snapshots into new AZ

Aurora
  - MySQL-compatible relational database engine, also compatible with PostgreSQL
  - Start with 10GB, scales in 10GB increments to 64TB,
  - Always maintains 2 copies of data in each availability zone, with a minimum of 3 availability zones
  - Aurora has read replicas -- can be Aurora replicas (up to 15) or MySQL (up to 5) read replicas
    - Automated failover is only available in Aurora replicas
  - Automated backups are always enabled on Amazon Aurora DB
  - You can share Aurora snapshots with other AWS accounts
  - RDS MySQL databases can create Aurora read replicas
  - Aurora writer nodes have different DNS endpoints than aurora reader nodes

ElastiCache
  - Web service making it easy to deploy, operate, and scale an in-memory cache in the cloud
  - Redis is Mutli-AZ, can do backups and restores of Redis
  - If you're just offloading database, then use Memached
  - If you need advanced data types, pub/sub capablities