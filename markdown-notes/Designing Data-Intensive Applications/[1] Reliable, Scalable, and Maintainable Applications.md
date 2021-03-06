# [1] Reliable, Scalable, and Maintainable Applications
<!-- toc -->

- [Reliability](#Reliability)
  * [Hardware errors](#Hardware-errors)
  * [Software Errors](#Software-Errors)
  * [Human Errors](#Human-Errors)
- [Scalability](#Scalability)
  * [Describing load](#Describing-load)
  * [Describing performance](#Describing-performance)
  * [Approaches for Coping with Load](#Approaches-for-Coping-with-Load)
- [Maintainability](#Maintainability)
  * [Operability](#Operability)
  * [Simplicity: Managing Complexity](#Simplicity-Managing-Complexity)
  * [Evolvability: Making Change Easy](#Evolvability-Making-Change-Easy)

<!-- tocstop -->
- _Reliability:_ System should work correctly even in the face of adversity. 
- _Scalability:_ As the system grows, there should be reasonable ways of dealing with that growth.
- _Maintainability:_ Different people should be able to work on the system productively. 

### Reliability
- _Faults:_ Things in a system that can go wrong, a system's ability to cope with those faults makes the system _fault tolerant_ or _resilient_.
- _Failures:_ System as a whole stops providing the required service to the user.
- Faults can lead to failures. Fault tolerance avoids systems from failing during faults. 
- Sometimes you'll cut corners because a budget won't allow the level of reliability you'd like, you just have to remain mindful and conscious about it. 

#### Hardware errors
- Because distributed systems are more widely available and are only conitnuing to grow, hardware faults are becoming more common. However, many of these faults have fault tolerences, so often times it is more important to create systems which can work with hardware fault tolerance. E.g. if a cluster needs to apply a security patch then the patch can be applied one node at a time, and if the system is built in a way that allows this then there will be no downtime.

#### Software Errors
- Software errors are harder to anticipate and more likely to cause system failure than uncorrelated hardware faults.
- A lot of these faults are exposed due to the assumption about its environment--eventually, that assumption stops being true and the system fails.

#### Human Errors
_How do we make our systems reliable in spite of unreliable humans?_
- Well-designed abstractions, APIs, and admin interfaces make it easy to do "the right thing" and discourage "the wrong thing"
- Decouple places where people make the most mistakes from places where they can cause failures. Provide fully featured sandbox envs where people can explore safely. 
- Test thoroughly (unit tests, integration tests, manual tests)
- Quick and easy recovery from human errors to minimize impact in the case of failure. (e.g. roll out new code gradually, provide tools to recompute data in case the old computation was incorrect)
- Detailed and cleared monitoring (telemetry)

### Scalability 
- Consider the question "if the system grows in a particular way, what are our options for coping with the growth?" "how can we add computing resources to handle this load?"

#### Describing load
-_Load parameters:_ Numbers and metrics used to describe load. 
- Twitter had two methods of delivering tweets to users. Twitter has switched to a hybrid of this approach where people with a large following use the first approach, and people with a smaller following use a second approach.:
  1. Write tweets to a global database, and when a user logs in, select from the global database all of the tweets from all of the user's followers
  2. Push tweets into home timeline caches--when a user posts a tweet, push the tweet into the user's cache. 

#### Describing performance
- What happens when you increase your load parameters but keep the system resources unchanged?
- When you increase a load parameter, how much do you need to increase the resources to keep performance unchanged. 
- _Throughput:_ The number of records we can process per second, or the total time it takes to run a job on a dataset of a certain size.
- Because response time varies with every single request, response time should not be measured as a single number but rather as a distribution of numbers. 
- Average response time is not very good metric because it doesn't tell you how many users experience the delay. Percentiles prove to be superior in this occasion.
  - Take list of response time, sorted from fastest to slowest, median is the halfway point (also known as the 50th percentile). (If median response time is 200ms, then half of the requests returned in less than 200ms and the other half in over 200ms)
  - In order to figure out how bad outliers are, look at higher percentiles --95th, 99th, and 99.9th. They mean that 95, 99, and 99.9 percent of all requests are higher than their associated values. 
- _Head of line blocking:_ Requests at the 'head of the line' block other requests. This is why it's important that when testing queueing and generating artificial load, the client sends requests independent of whether or not the service has responded yet. 

#### Approaches for Coping with Load
- Growing loads require architects to rethink their system architectures.
- _Elastic systems:_ Can add computing resources when load increase is detected. Useful when load is highly unpredictable. 
- Usually highly-scalable architectures are built from general-purpose building blocks. 

### Maintainability
In order to avoid creating legacy software, it is important to pay attention to three design principles:
- _Operability:_ Making it easy for software teams to keep the system running smoothly.
- _Simplicity:_ Making it easy for new engineers to understand the system. 
- _Evolvability:_ Make it easy for new engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change.

#### Operability
Good operability means making routine tasks easy. Ways to do this include:
- Providing visibility into the runtime behavior and internals of the system with good monitoring.
- Providing good support for automation and integration with standard tools.
- Avoiding dependency on individual machines.
- Providing good documentation 
- Providing good default behavior
- Self-healing where appropriate, but also giving admins manual control over the system state when needed.
- Exhibiting predictable behavior, minimizing surprises.

#### Simplicity: Managing Complexity
- Making systems complex makes maintenance hard, always do your best to keep it simple. 
- _Accidental complexity:_ Software problems arising from the implementation of the software.
- _Abstraction:_ Good abstraction can hide a great deal of implementation behind a clean, simple-to-understand façade and can be used for a wide range of applications. Take large part of systems and turn them into well-defined, usable components.
  - Delta seems to try to do this with their Cassandra integration library but it also seems like they do a poor job; why didn't they just use Spring?

#### Evolvability: Making Change Easy
- Keeping systems simple usually make it easier to respond to change. 
- "The ease with which you can modify a data system, and adapt it to changing requirements, is closely linked to its simplicity and its abstractions."
