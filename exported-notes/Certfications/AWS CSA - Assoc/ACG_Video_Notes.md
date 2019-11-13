# CSA Exam - EC2

## EC2 101

### Summary
- Underlying hypervisors for EC2: Nitro and Xen
- 4 Pricing Models:
  - On Demand: Fixed rate by the hour, no commitment
  - Reserved: Capacity reservation
  - Spot: Bid for the price
  - Dedicated Hosts: Dedicated hosts for your EC2 instances
- EBS: Virtual Hard Disk Drive in cloud
  - Termination Protection is turned off by default, EC2 instance gets deleted then EBS volume gets deleted
- EBS Root Volumes of DEFAULT AMIs cannot be encrypted
- Additional volumes can be encrypted
- Security group, all inbound traffic is blocked, all outbound is allowed. Security groups are STATEFUL, meaning inbound and outbound traffic are blocked
- Cold HDD is cheapest option. 
- EBS Snapshots are point in time copies, are incremental
- Volumes will always be in same AZ as an EC2 instance

- _On Demand_ - Pay a fixed rate by the hour
  - Low cost and flexible
  - Short term, spiky, unpredictable workloads
- _Reserved_ - Capacity reservation at a significant discount
  - Stead state or predictable usage
  - Standard Reserved: Can't change instance type
  - Convertible Reserved: Can change instance type
  - Scheduled Reserved Instances: 
- _Spot_ - Bid for the price you want for instance capacity
  - Flexible start/end times
  - If you terminate the instance yourself, AWS will still charge you for the hour the instance ran.
- _Dedicated Instances_: Pretty much Amazon metal assigned to you
- CloudWatch detailed monitoring monitors the instance more frequently than once every 5 minutes (every minute)
- Termination protection is turned off by default
- Default action is for root EBS volume to be deleted when instance is terminated. Can be changed to keep virtual HDD
- EBS Root Volumes can be encrypted, you just have to create a snapshot of a root volume, create an encrypted copy of the snapshot, and the create an image with the encrypted root volume
  - Additional volumes can be encrypted
- Roles are universal, you can use the role from any region.
- Get instance metadata at http://169.254.169.254/latest/meta-data/

### Security Groups
- Can't block IP addresses or ports using Security Groups
- Everything is blocked by default with security groups
- An EC2 instance can have more than on security group
- Default:
  - All inbound traffic is blocked
  - All outbound traffic is allowed
- Security Groups are stateful, meaning we don't have to change things for inbound and outbound ports. An inbound rule will allow traffic in and back out again.

### EBS 101

- Elastic Block Store: Virtual hard disk in cloud. 
  - Each volume automatically replication within its AZ
  
  #### EBS volume types
  1. General Purpose SSD: Balances price and performance, max IOPS/volume: 16,000 
    - Balances price and performance
  2. Provisioned IOPS (SSD): Max IOPS/Volume: 64,000
    - Most performant
  3. Throuhgput Optimized HDD: IOPS/volume: 500
    - Low Cost HDD
  4. Cold Hard Disk Drive: Max IOPS/Volume: 250
    - Lowest cost
  5. Magnetic
    - Legacy HDD

  - Volumes can be modified, you can change the volume type and size.
  - Volumes can be moved to another AZ by creating a snapshot and creating a new image. The snapshot lives in S3.
  - Hardware Assited Virtualization allows you to create different types of EC2 instances
  - AMIs can also be copied across regions by copying the AMI from one region to another, then launching that AMI in the new region
  - Root volume for an EC2 instance is deleted when the EC2 instance is terminated, added volumes are persisted
  - AMIs can be created from both volumes and snapshots
  - Volumes will always be in the same AZ as the EC2 instance

### EBS vs Instance Store

- All AMIs backed by instance store or EBS
- The root volume is either an instance store or EBS
  - Instance store volumes can't be stopped--only rebooted or terminated. If the hypervisor fails, you will lose all data in an instance store since it is ephemeral

### Encrypted Root Volumes

- Root volumes are now able to be encrypted for EBS. 
- Unencrypted volumes can be encrypted, but they take on another process:
  - Create volume snapshot
  - Copy snapshot
  - Encrypt snapshot
  - Create image
  - Instance created from image (AMI) will need to be encrypted 
- Snapshots of encrypted volumes are encrypted automatically

### CloudWatch 101

- Cloudwatch monitors resource and compute performance 
- Cloudtrail is like CCTV--it record user actions

### EFS

- Supports NFSv4
- Only pay for the storage you use
- Elastic File Storage
- One file storage can be attached/mounted on to more than one EC2 instance
- Read after write consistency

### EC2 Placement Groups

- A way of placing EC2 instances
  - Clustered Placement Group
    - Grouping of instances within single AZ
    - Put instances very very close to eachother
    - Low network latency, high network throughput 
  - Spread Placement Group
    - Group of instances placed on distinct racks/underlying racks
    - Can exist within different AZs within a region
    - Better for individual instances, designed to protect EC2 instances from hardware failure
  - Partitioned
    - Multiple instances can be in a partition group, partitions are in different racks. Partition 1 has instances A and B, but sits in a different rack than Partition 2.
    - Can exist in different AZs within a region
    - Multiple EC2 instances 
- Placement group names must be unique
- Existing instance can't be moved into a placement group, but you can relaunch an AMI and then launch a new instance from the AMI into the placement group.