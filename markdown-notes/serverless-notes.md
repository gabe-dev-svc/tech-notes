# Serverless Computing 

_The point of these notes is to server as a reference for a Lunch-n-Learn over serverless computing, answering some of the questions people may have._

Scope is really limited to a "Serverless 101". Going to be a definition of serverless and possible applications thereof, then a demo of an application hosted all serverless. 

__Goal date for lunch-n-learn: 9/4/2019__

#### Outline
- What is serverless computing?
  - Definition
  - History(?)
  - What are some of the advantages serverless computing has?
  - Caveats that serverless computing has
  - Major cloud providers offering serverless computing
- Overview of AWS Serverless services
  - Amazon S3
  - Amazon DynamoDB/Amazon Aurora Serverless
  - AWS Lambda
  - Amazon Kinesis 
  - Amazon SNS
  - Amazon SQS
- Serverless application use cases
  - Data processing
  - Entire web application
- Demo 
- Pricing
- Questions

## Questions that I have and want to get answered about serverless

- What is serverless?
_Serverless allows technologists to run their  computing services* on a cloud-provider's architecture (in our examples, AWS) without having to worry abaout a majority off the adminstrative tasks that come with using their own servers or even managing their own virtual servers._
> Serverless applications don’t require provisioning, maintaining, and administering servers for backend components such as compute, databases, storage, stream processing, message queueing, and more. 

- Why use serverless?
Increased agility - With less operational tasks, you're able to allocate more of your resources (time, people, money) to development. The computing services needed for applications are already up and running, it's just a matter of writing the code and using them.
  - Typically cloud providers offer scaling based on metrics defined by you. Rather than scaling based on number of users, resources (compute, throughput, memory) can be monitored and increased at certain thresholds.
  - With serverless, you pay for throughput/execution. 

- What are some of the caveats to  using serverless?

- What is AWS Lambda?
[Here](#AWS-Lambda)
- What are some of the databases available via serverless?
- What about other applications other than web applications?
- How do you handle security?
- How would you build a serverless REST endpoint?

## Hosting a Serverless Web Application
Use:
- [AWS Lambda](#AWS-Lambda)
- [Amazon API Gateway](#Amazon-API-Gateway)
- Amazon S3
- [Amazon DynamoDB](#Amazon-Dynamo-DB)

## AWS Lambda
Compute services allowing users to run code without provisioning or managing servers. Code is executed only when needed and scales automatically. 

__Benefits__
- Pay only for the compute time consumed. 
- Languages Supported: 
  - Node.js (JavaScript)
  - Python (3.6, 3.7, 2.7)
  - Ruby
  - Java 8
  - Go
  - .NET Core (2.1 [C#, PowerShell Core 6.0], 1.0 [C#])
  - Other langauges can be used if implementing a custom runtime, out of scope tho.
- Lambda is usually ran in response to events (e.g. changes to data in Amazon S3 or DynamoDB, run code in response to HTTP requests, invoke code using API calls made using AWS SDKs)

### Lambda Security
- IAM (Identity and Access Management) is used to manage access to Lambda API and resources like functions and layers. 
- Permissions policy is created granting access to certain groups, users, and services that give them access to that Lambda function.
- Manage permissions in a permission policy for users and services to use Lambda. 
- Lambda functions also have policies (execution roles) which grant it permission to access AWS resources and services. 


## Amazon API Gateway
AWS Services creating, publishing, maintaining, monitoring, and  securing REST and WebSocke APIs at any scale. 
- APIs are HTTP-based, meaning they support HTTP verbs (GET, POST, PUT, PATCH, and DELETE)
- A reverse proxy, of a sort, which routes requests to certain events, AWS services, or private applications. 
- > API Gateway acts as a "front door" for applications to access data, business logic, or functionality from your backend services, such as workloads running on Amazon Elastic Compute Cloud (Amazon EC2), code running on AWS Lambda, any web application, or real-time communication application
- Powerful, flexible authentication mechanisms, such as  AWS IAM policies, Lambda authorizer functions and Amazon Cognito user pools. 

## Amazon Dynamo DB
Dynamo DB is the serverless, NoSQL offering provided by AWS. `tables` are collections of `items` which are collections of `attributes`. 
- `tables` use primary keys to uniquely identify each item, and `secondary indexes` to provide more querying flexibility. 
- `Dynamo DB Streams` stream data modification events. 
- Dynamo DB tables are schema-less, however, they do use primary keys as unique identifiers.
- Most attributes are scalar (e.g. strings and numbers), however, nested attributes are used and can go as far as 32 levels deep.
- Composite primary keys can be used. (e.g. in a `music` table the primary key can be a combination of `Artist` and `SongTitle`)
- Types of Primary Keys:
  - __Partition Key:__ Simple primary key composed of one attribute (known as the partition key.)
    - Value is used as an input to an internal hash function, the result determines the _partition_ in which the item will be stored. 
  - __Partition key and sort key:__ Referred to as composite primary key, key composed of two attributes. First attribute is partition, second attribute is sort key. 
    - The partition key works the same as described above, however, all items with the same partition key value are stored together in sorted order by sort key value. 
- Secondary Indexes allows data to be queried using an alternative key (in addition to the primary key)
  - __Global secondary index:__ Index with partition/sort keys that can be different from those on the table.
  - __Local secondary index:__ Index with the same partition key as the table, but different sort key. 
  - Limit of 20 global secondary indexes and 5 local secondary indexes per table.
  - Secondary indexes are almost kind of like child tables. Each index belongs to a table (`base table`). Indexes are maintained by DynamoDB automatically, when CRUD happens on the base table, DynamoDB performs the corresponding operation on the index that belongs to that table. 
  - When an index is created, attributes which will be projected from the base table to the index are specified. 

### Dynamo DB Streams
Optional feature capturing data modification events in DynamoDB tables. Events appear in near-real time and in the order in which they occured. 
- _Stream record:_ Representation of the event, events include:
  - Writes: Stream captures an image of the entire item, including all of its attributes. 
  - Updates: Stream captures before and after image of any attributes that were modified in the item. 
  - Deletion: Stream captures an image of the entire item before it was deleted. 

### DynamoDB API

- _Control plane_: Create and manage DynamoDB tables and objects that are dependent on tables (indexes, streams, etc)
  - `CreateTable`
  - `DescribeTable`
  - `ListTables`
  - `UpdateTable`
  - `DeleteTable`
- _Data plane_: CRUD actions on data in a table, some operations also let you read data from a seconday index
- `PutItem`: Write single item to a table. Primary key attributes must be specified.
- `BatchWriteItem` Writes up to 25 items to a table. More efficient than multiple `PutItem`s
- `GetItem`: Retrieves single itme from table. Specify the primary key for the item you want.
- `BatchGetItem`: Retrieves up to 100 items from one or more tables.
- `Query`: Retrieves all items that have a specific partition key. Partition key value must be specified. Can retrieve entire items or just a subset of their attributes. Condition can be applied to the sort key values so that you only retrieve a subset of the data that has the same partition key. 
- `Scan:` Retrieves all items in the specified table or index. Can return entire items, or just a subset of their attributes. Can apply filtering condition to return only the values that are of interest. 
- `UpdateItem`: Modifies item attribute(s). Primary key must be specifiedfor the item that is to be modified. Can perform conditional updates so that the update is only successful when a user-defined condition is met. Can implement atomic counter which increments and decrements a numeric attribute without interfering with other write requests. (Locking)


## Demo Components
- UI static hosted on S3
- Data stored in Dynamo DB
- API Gateway serving as reverse proxy
- Lambda function used to process images (?)