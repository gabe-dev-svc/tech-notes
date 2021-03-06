# CSA Exam - HA Architecture

## Load Balancers
- Three types:
  - Application load balancer
  - Network load balancer
  - Classic load balancer
- Application Balancer:
  - Intelligent
  - Best suited for load balancing of HTTP and HTTPS traffic
  - Operate at Layer 7 and are application-aware
  - Intelligent, can create advanced request routing
- Network Balancer:
  - Performant
  - Best suited for load balancing TCP traffic, has fixed IP address
  - Operates at the connection level (layer 4)
  - Capable of handling millions of reqs/second
- Legacy ELB
  - Cost-effective, basic load balancing
  - Can load balance HTTP/HTTPS and use Layer 7 specific features
  - Can also use strict layer 4 load balancing for applications relying closely on Layer 4
- `504` means application stopped working
- `X-Forwarded-For` HTTP header forwarding the IP address of original requestor 

## Load Balancers and Health Checks
- Internal load balancers are inside private subnets
- ELBs have health checks with response timeouts and intervals
- ELBs are given DNS names, never an IP address
- When a service is recorded as unhealthy, it is removed out of the load balancing pool
- Classic load balancers are "dumb", application load balancers are "intelligent"
- Target group: group of instances which the load balancer will balance load to.
- ALBs are more intelligent because you're able to add rules

## Advanced Load Balancer Theory
- Sticky sessions: 
  - Allows you to bind a user's session to a specifc EC2 instance. 
  - Ensures all requests from the user during the session are sent to the same instance.
- Cross Zone Load Balancing:
  - Allows load balancers to send traffic across availability zones 
- Path Patterns:
  - Create a listener with rules to forward requests based on the URL path.

## Launch Configurations and Autoscaling Groups
- To create autoscaling group, you need a launch configuration
- Launch configuration: Saved configuration for EC2 instance launches.
- Autoscaling groups will start to deploy instances based on launch configuration
- Can put launched instances behind an ALB with healthchecks
- Scaling policies are created to adjust the capacity of group
  - Scale between `x` and `y` based on CPU or memory utilization 
- Autoscaling group detects fallen instances, initializes new ones based on the `desired` value

## HA Architecture
- Always plan for failure
- [Netflix Article Over Failure](goo.gl/UR2nzM)
- Use multiple AZs and Multiple Regions where you can
- Scaling out: Autoscaling groups -- add more EC2 instances
- Scaling up: increase resources inside EC2 instances
- Review Multi-AZ vs Read Replicas and difference between S3 storage classes.
  - Multi-AZ: Availability and disaster recovery
  - Read Replicas: Performance
## Creating a HA WP Site
- CloudFront: Delivers content through edge locations. 
  - User is routed to that edge location whenever they request the content
  - If the content is already in the edge location, CloudFront delivers it immediately. Otherwise, CloudFront goes and gets it from the origin defined and caches it in multiple edge locations.
- `htaccess` allows for content rewrites. Configuration file for Apache Web Server, can be used to alter the configration of the Apache Web Server.
- Can redirect files uploaded files to be uploaded to S3 for better redundancy instead of locally, and then force cloudfront to serve files instead of S3 using rewrite rules in configuration files like `htaccess`.
- `aws s3 sync <source> <destination>` `--delete` delete files not present

## Elastic Beanstalk
- 