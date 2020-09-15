# CSA Exam - Applications

## SQS - Simple Queue Service
- Messages can be up to 256 KB of text, you can go over and SQS will store up to 2 GB in S3
- Can also trigger autoscaling groups
- Standard Queues:
  - Handles a lot of queues
  - Best effort ordering
  - Messages can be delivered more than once
- FIFO Queues:
  - Ordered - first in first out
  - Message delivered once, duplicates are not introduced
  - Limited to 300 transactions/second
- SQS is always poll-based
- Message can be kept in queue from 1 minute to 14 days, default retention is 4 days
- Visibility time out: amount of time the message is invisible in the SQS queue after a reader picks up that message. If the job is not processed within the visibility time out, the message won't be deleted and could result in messages being delivered twice. 
  - Maximum time out is 12 hours
- Long polling: Doesn't return until the message arrives - _cheaper_ 
- Short polling: Returns even if there is no message 

## SWF - Simple Workflow Service
- Web service that makes it easy to coordinate/orchestrate work across a bunch of different applications
- Tasks can be performed by executable code, web service calls, human actions, and scripts
- SWF is service (and human) orchestration
- Workflow execution can last up to 1 year
- Presents a task-oriented API
- Tasks are assigned only once and is never duplicated.
- Tracks all tasks and events in an application
- Actors
  - Workflow starters: initiate a workflow
  - Deciders: Controls the flow of activity tasks
  - Activity workers: Carry out activity tasks

## SNS - Simple Notification Service
- Send notifications from cloud
- Allows push notifications to mobile devices, also to HTTP endpoints, text messages, email, or SQS -- no polling in SNS
- Subscribe to topic, allows multiple recipients to dynamically subscribe to identical topics.
- Stored redundantly across multiple AZs
- Inexpensive 

## Elastic Transcoder
- Convert media files from original source format into different formats
- Provides transcoding presets for popular output formats
- Pay based on minutes you transcode and on the resolution

## API Gateway
- "Front door" for applications to access data, business logic, or functionality from your back-end services.
- Make call to API Gateway, API Gateway calls Lambda, DynamoDB, or EC2
- Expose HTTPS endpoints to define a RESTful API
- Scales effortlessly
- Run efficiently with low cost
- Send API endpoint to a different target
- Throttle requests to prevent attacks
- How to deploy
- Deploy API to a stage
  - Uses API Gateway domain by default
  - Can use custom domain
  - Now supports AWS Certificate Manager: free SSL/TLS certs
- API Caching is available. Reduce # of calls made to endpoints. Responses are cached in API Gateway.  
- API Gateway has CORS -- enable CORS for the origins you wish you allow

## Kinesis 
- Kinesis: Platform on AWS to send streaming data to. Makes it easy to load and analyze data.
  - Kinesis Streams:
    - Place to store stream data. Retention period is 24 hours - 7 days. 
    - Data contained in shards.
      - 5 transactions per second for reads, up to maximum total data read rate of 2MB per second
      - 1,000 records per second for writes, up to a maxmium total data write of 1MB per second 
      - Data capacity of your stream is a function of the number of shards that you specify for the stream. Total capacity of the stream is the sum of the capacities of its shards.
    - Consumers can go in and analyze data inside shards. 
  - Kinesis Firehose:
    - No persistent storage, data is analyzed as it's brought in usually in a Lambda. The result is stored to S3 or to Elasticsearch Cluster.
  - Kinesis analytics:
    - Works with both Firehose and Streams
    - Analyzes data on the fly inside either service (in Kinesis), stores data in elasticsearch cluster, Redshift, of S3
- Streaming data: data generated coninuously by thousands of data sources, typically send data records simutaneously and in small sizes.


## Web Identity Federation and Cognito
- Web Identity Federation: Users gain access to AWS resources after they have successfully authenticated with a web-based identity provider.
- Cognito provides Web Identity Federation
  - Sign-up and sign-in to your apps
  - Synchronize user data for multiple devices
  - Acts as an identity broker between your application
- User pools: user directories used to manage sign-up and sign-in functionality for mobile and web applications. 
  - All about usernames and password
- Users can sign in directly to the User Pool or using Facebook, Amazon, or Google. Cognito assigns JWT.
  - All about authoriziation
- Identity pools provide temporary AWS credentials to access AWS services like S3 or Dynamo DB via IAM roles.
- Cognito synchronizes data using silent SNS push notifications
- User authenticates first with WebID provider and receives an authentication token, exchanged for temporary AWS credentials alllowing them to assume an IAM role.