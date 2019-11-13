# CSA Exam - S3 and Identity Management

- __IAM__: Identity Access Management
  - Centralized control of AWS
  - Shared access to your AWS account
  - Granular permissions
  - Identity Federation (users can log in with Facebook credentials, AD, etc)
  - Multifactor Authentication
  - Provides temporary access for users/devices/services when necessary. 
  - Allows to set up password rotation policy
  - Integrates with many AWS Services
  - Supports PCI DSS Compliance
- __Users__: End users
- __Groups__: Collection of users
- __Roles__: Roles are created and assigned to _AWS Resources_
- __Policies__: Documents (policy documents) give permissions as to what a User/Group/Role is able to do. The JSONS that defines what user/groups/roles are able to do.
- Enable MFA for the root account to add layer of security
- User Access Types:
  - _Programmatic Access_ created access key ID and secret access key to interact with AWS API, CLI, SDK, and other development tools. 
  - _AWS Management Console Access_ - access to the AWS Management Console
- Always set up MFA on root account
- It's possible to create and customize password policies

## Setting Up a Billing Alarm

- Billing alarms are created within CloudWatch under Billing Alarms

## Amazon S3 101

- S3: __S__ imple __S__ torage __S__ ervice. 
- Safe place to store files
- Files can be from 0 Bytes to 5 TB
- Unlimited storage
- Object-based meaning it allows you to upload files, containing: 
  - Key: The name of the object
  - Value: Data of the file, made up of sequence of bytes
  - Version ID: Self explanatory
  - Metadata: Self explanatory
  - Subresources: 
    - Access Control Lists
    - Torrent
- Buckets must be unique globally because web addresses are created for the buckets created
- Successful uploads will receive a 200 HTTP Code
- You are able to make objects public by changing the bucket settings to allow public objects.
- Tiering can be defined on an object-level or a bucket-level.
- Control access to buckets using Bucket Access Controll List (ACL) or using Bucket Policies
- Once a bucket enables versioning it cannot disable versioning, only suspended. 
- Uploading a new version of the file resets the public access settings, so the files will not automatically be made public. 
- Previous versions are still visible by clicking the link.
- If you delete a versioned file, a delete marker is placed if you click the object itself and not a specific version. If you try to delete a specific version, the the version itself deletes.
### S3 Storage Tiers:
_First byte latency is milliseconds for all tiers except Glacier_
 - Standard: 99.99% Availability, 99.99...(11 9s)% durability. Stored redundantly across multiple devices in multiple facilities, designed to sustain the loss of 2 facilites concurrently.
 - S3 - Infrequently Accessed: Data that is accessed less frequently but requires rapid access when needed. Lower fee but charged retrieval fee.
 - S3 One Zone - Infrequently Accessed: Lower-cost option for infrequently accessed data, do not require the resilience of mutliple Availability Zones
 - S3 - Intelligent Tiering: Designed to optimize costs by automatically moving data to the most cost-effective access tier.
- S3 Glacier: Secure, durable, low-cost storage class for data archiving. Competitive with or cheaper than on-premises solution.
 - S3 Glacier Deep Archive: Amazon S3's lowest-cost storage class where a retrieval time of 12 hours is acceptable.

### How does data consistency work for S3?
- __Read after Write consistency for PUTS of new Objects:__
  - Able to read a file immediately after writing it
- __Eventual Consistency for overwrite PUTS and DELETES__
  - It can take some time for the overwrite to propogate. It could take a second to update.
  - "If you update an existing file or delete a file you may get the older version or you may not."
- Amazon guarantess 99.99...% (11 9s) availability 
- S3 offers:
  - Tiered Storage
  - Lifecycle Management
  - Versioning
  - Encryption
  - __MFA Delete__
  - Secure data using Access Control Lists and Policies
- Billing: 
  - Storage: The more you store, the more you're billed
  - Requests
  - Storage Management Pricing
  - Data Transfer Pricing
  - Transfer Acceleration
    - Enables fast, easy, and secure transfers of files over long distances between your end users and an S3 bucket. Takes advantage of CloudFront's globally distributed edge locations.
    - Files get uploaded to an edge location, goes through the backbone network, speeds up uploads and downloads.
  - Cross-Region Replication
    - Uploading an object to one bucket in one region automatically gets uploaded to another bucket in another region

### S3 Security and Encryption
- ALl buckets are private by default. 
- Bucket policies: bucket-level
- Access Control Lists: can go down to the object-level.
- __Encryption in Transit__: Achieved by SSL/TLS
- __Encryption at Rest (Server Side)__: Achieved by:
  - S3 Manged Keys - SSE - S3
  - AWS Key Management Service, Managd Keys - SSE - KMS
  - Servier Side Encryptiuon with Customer Provided Keys - SSE-C
- __Enecryption at Rest (Client Side Encryption)__: Uploading encrypted objects

### S3 Lifecycle Management
- Lifecycle rules can be created for current versions are previous versions.
- After `x` amount of days, the current or previous (or both) versions can move to the tier specified in the rule.
