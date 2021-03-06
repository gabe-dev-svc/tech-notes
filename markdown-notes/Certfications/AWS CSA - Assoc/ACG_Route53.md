#CSA Exam - Route 53


## DNS 101
- Route 53: Amazon DNS service 
- ICANN enforces uniqueness of domain names across the Internet
- Amazon is now a domain name registrar
- DNS Records
  - `A` stands for Address - What's the IP Address of this domain?
  - `NS Records` - Name server, which DNS server is authoritative for that domain record?
  - `SOA Records` - Start of Authority, stores information about the name of the server that supplied the data for the zone.
    - Stores name of the server that supplied data for the zone, among other details
  - `CName Records` - stands for canonical name
    - Can be used to resolve one domain name to another
    - Can't be used for naked domain names (missing `www`)
  - `Alias Records` work like `CName` records
    - Alias records can be created at the top node of a DNS namespace (zone apex)

## Domain Name Registration
- You can buy domain names online via Route 53
- It can take up to 3 days to register depending on the circumstances

## Routing Policies Available on AWS
- Whenever your instance IP addres changes you need to update the Route 53 records
- Simple Routing
  - You can only have one record with multiple IP addresses. 
  - Route 53 picks an IP address randomly.
- Weighted Routing
  - Allows you to weight traffic to different areas/regions
  - Create multiple record sets, assign Set ID to each record with the weight.
  - Route 53 allows health checks which in turn allows you to disassociate records if health checks fail
- Latency-based Routing
  - Allows routing based on latency for end user
  - Chooses route with lowest latency
- Failover Rouing
  - Have two routes, one active one passive. All traffic is sent to active unless the health check fails, then the passive route is taken sending it to the passive (failover) server
- Geolocation Routing
  - Choose where traffic is sent based on the geolocation of users.
  - Routes traffic based on user's location
- Geoproximity Routing
  - Must use Route 53 traffic flow
  - Route traffic to your resources based on geographic location of users and resources
- Multivalue Answer Routing
  - The same as simple routing (a record can have multiple IP addresses) but the endpoints can have health checks