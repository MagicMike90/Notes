# Microservice notes

## Domain-drive design

It is very important to note that OOP is not only inheritance, interfaces, or anything else of the type. OOP's main ideas are as follows:

- Code alignment with the business
- Favoring of reuse
- Minimal coupling

three are more prominent for efficient microservices:

- Context maps: These are the communication paths between microservices with appropriate interactions between microservices teams. After the analysis of the areas are already defined, the team can choose to be dependent on another team for domain language.
- Anti-corruption layer (ACL): This is the function that translates foreign concepts for an internal model to provide loose coupling between the domains.
- Interchange context: This provides an environment for both teams and discusses the meaning of each foreign term and translates the languages of microservices.

## Independent deploy, update, scale and replace

### Update

- Never share libraries between microservices
- Strong delimitation of microservice domains
- Establish a client-server relationship between microservices
- Deploy in separate containers

### Scale

**The Scale Cube**: 3 axis

- x-axis: horizontal decomposition: with the same application server replicated n times in full and in a balanced order of 1/n.
- y-axis: functional decomposition: a verb or route is used by the balancer to identify where to go with the request.
- z-axis: data partitioning: is very similar to the x-axis when it comes to scalability structure, as it distributes exactly the same code on each server. The big difference is that each server responds to a specific subset of data

## Communication

|              |       One-to-One        |             One-to-Many |
| ------------ | :---------------------: | ----------------------: |
| Synchronous  |    Request/response     |                       - |
| Asynchronous |      Notification       |        publish/subsribe |
|              | Request/ async response | publish/async responses |

### Synchronous

- HTTP
- TCP
- WebSockets
- Sockets
- RPC
- SOAP

### Asynchronous

For this approach, the message broker is just perfect. Some software applications appear a good choice for message brokers, such as RabbitMQ, ActiveMQ, ZeroMQ, Kafka, and Redis. Each of these options has its own peculiarities, some are faster, others are more resilient. Again, the business setting is going to determine which technology is used.

### Mobile vs web endpoints

Problems such as speed and weight information in the web world are not very common; we cannot say the same for the mobile world.

### Caching at the client level

request only passes to be processed on the backend, if really necessary. In other words, it tries to block direct access to the backend to requests that have already been implemented in the recent past.

### Throttling for your client

- Number of requests per minute from the same client
- Number of requests per second from the same client
- Number of requests per minute from the same client for similar information
- Number of requests per second for the same client for the same information

### Identification of an anemic domain

- The microservice cannot perform the tasks itself with only the data received
- The microservice needs to fetch data in more than one endpoint to perform a task
- The microservice does not have a self-sufficient entity model
- The microservice waits for the completion of a task in another microservice to follow up what you need to do
- The microservice needs to share resources with other external microservices; these resources can be cached to the sample database

If the microservice being developed is one of those items, then it can be a weak area. If a microservice has two or more characteristics of those listed, then it is definitely an anemic domain.

Anemic domains are very harmful to the microservices ecosystem, because they have a tendency to be multiplied in order to correct the technical debt generated by the deficiency in the composition of their respective domains.

### Fat domain - AAA (Authentication, Authorization, and Accounting

The division of this fat domain can be held in two parts; the first part is AAAService and the second is UserService. Another approach is the AAA responsibility for a gateway API. The functional scalability and features of implementation with these separate domains is much more interesting for the growth of the product as a whol

## Things to consider

Cost and scability:

- Programming languages
- Microservices frameworks
- Binary communication
- Message broker
- Caching tools
- Fail alert tools
- Locale proof performance

## Death Start

The Death Star is an anti-pattern where there is communication between the recursion microservices, and making progress becomes extremely complicated or expensive for a product.

## Message broker - Async communication between services

why not use this messaging for all types of communication between microservices?

The answer to this question is quite simple. A message bus is a physical component within the stack of microservices. It needs to be scaled just like any other physical component-based data storage and cache. This means that with a high-volume message, the synchronous mode of communication could be committed to an unwanted delay in the responses of the processes.

### Tools

- ActiveMQ
- RabbitMQ
- Kafka

#### Caching tools

- Memcached
  classic process of using cache, Memcached is simple and practical to use. The performance of Memcached is fully linked to the use of memory. If Memcached uses the disc to register any data, the performance is seriously compromised; moreover, Memcached does not have any record of disk capacity and always depends on third-party tools for this.
- Redis

#### Fail alert tools

four major points of failure when it comes to microservices

- Performance - New Relic and Datadog
- Build - Jenkins, Travis
- Components - Nagios and Zabbix are also very useful for making aid work to health check endpoints.
- Implementation failures - Sentry
  - See the impact of new deployments in real time
  - Provide support to specific users interrupted by an error
  - Detect and thwart fraud as it's attempted: unusual amounts of failures on purchases, authentication, and other critical areas
  - External integrations

### Locale proof performance

- Apache Benchmark
- WRK
- Locust

## Internal Patterns

- Developing the structure
- Caching strategies
- CQRS – query strategy
- Event sourcing – data integrity

## CQRS(Command Query Responsibility Segregation)

As the name implies, it is about separating the responsibility of writing and reading of data. CQRS is a code pattern and not an architectural pattern.

`Will just scaling the application servers solve all our problems?`

- Deadlocks, timeouts, and slowness mean that your database may be in too much demand.
- Complex queries can be performed to obtain database data. ORMs can add even more complexity to the data filtering process by mapping entities and filtering data by using joins in different tables.
- Content obsolescence could be true

The CQRS teaches us the division of responsibility for writing and reading data, both conceptual and using different physical storages. This means that there will be separate means for recording and retrieving data from the databases. Queries are done synchronously in a separate denormalized database, and writes asynchronously to a normalized database. Caching first is still a type of CQRS implementation at the conceptual level.

**Command** will be responsible for modifying the state of the data in the application, **Query** is the operation responsible for retrieving information from the database.

**Synchronization**: The following are some strategies to keep the foundations of reading and recording synchronized, and it is necessary to choose the one that best meets your scenario:

- Automatic updating: All changes in the state of a given recording database raise a synchronous process to update on the bench
- Update possible: All state changes of a given recording database trigger an asynchronous process to update the reading bank, offering an eventual data consistency
- Controlled update: A regular process and schedule is raised to synchronize the databases
- Update on demand: Every query checks the consistency of the read base compared to the recording, and forces an update if it is out of date

Any update is one of the most used strategies, because it assumes that any given displayed data may already be out of date, so it is not necessary to impose a synchronous update process.

**Queueing**: Many CQRS implementations may require a message broker for the processing of commands and events.

## Event sourcing – data integrity

- Each change in the current state of your database would be a new event in a stream that only allows inclusion.
- Each update on the table would generate a new line, the change of status
- uses the append only model for database records

## Aggregator Microservice Design Pattern

Best practices:

- Segregated database: This allows us to better scale our application, especially in the data storage layer.
- Microservice encapsulation: This divides the microservices into two layers—Public Facing Services and Internal Services. Such a division allows for greater flexibility with respect to the signature microservices, as Internal Services can be modified more easily.
- Applied CQRS: With CQRS, unnecessary stress points on the application were removed.
- Applied event sourcing: With event sourcing, we are conducting a stream of information from a news article. This gives us a real vision of the history of each news article.
- Applied pattern very scalable: With a strong pattern and understanding of how to scale the aggregator pattern, we have a clear vision of how to avoid anti-patterns.

Pros:

- Scalability of both the x-axis and z-axis
- Tunneling microservices
- Microservices signature flexibility to Internal Services
- Providing a single access point for microservices

Cons:

- Complexity to orchestrate data
- Bottleneck anti-pattern
- Latency in communication between microservices

## Proxy design pattern

- Dumb proxy - the only goal is to provide a single endpoint to facilitate the application clients and encapsulate direct access to the routes of microservices.
- Smart proxy - The most commonly seen task being executed by a Smart proxy is the content modification.

Pros:

- Practical data consumption by the application clients
- Ease of implementation
- Possibility of good programming techniques at proxy level, such as caching
- Encapsulation of access to microservices
- Control and diversion of requests

Cons:

- Bottleneck
- Inappropriate change of response
- Obstruction in the identification of overload

## Chained Microservice Design pattern

**Big Ball of Mud Anti-patern**:developing microservices that we do not define well in the domains, which makes microservices dependent on one another to complete trivial tasks. This type of error generates a series of unnecessary calls between the microservices, creating complex problems of being corrected, such as latency and, in the worst cases, cyclic-deployed dependency.

Main characteristics:

- **Poorly defined domain**: The domain is badly defined, forcing a direct connection with other microservices. Access points to the microservice do not require enough data to process a task, which forces the inference of data, searching in other microservices.
- **Mandatory direct communication**: When direct communication between microservices is mandatory for most tasks or for all tasks, there is a problem. This indicates that the microservice is anemic or that the application as a whole is poorly developed.
- **Deploy clustered**: When a microservice cannot be sent for production because it needs another microservice all together, or when the change of internal signatures of a microservice causes others to collapse, there is a Death Star anti-pattern issue. Creating business dependencies between microservices means that processes are not automated and fluid.

_Solution_:
Correlation ID: A simple way to implement correlation ID using HTTP would be to send a UUID in the header of the requests and use this UUID as an identifier to write the logs.

### Purest microservices

The microservices in your business design should be pure. This means that a microservice must be extremely small in its domain and fully capable of performing its function without outside interference from other microservices.

Pros:

- Practical implementation
- Dynamism for business
- Independent scalability
- Encapsulation of access to microservices

Cons:

- The possibility of latency points
- The difficulty in understanding data ownership
- The difficulty of debugging

## Branch Microservice Design Pattern

Rules:

- The composition of the response using direct call chains cannot extend one direct call to another microservice
- If more values are required for the response, an aggregation logic is created that will trigger concurrent requests for as many chains as needed

Internal communication within a microservice can be worked on in the following three ways:

- Sequential: No concurrency or parallelism. This means that when we have to send messages from the commands to the microservices, the entire process will be a sequence. If you need to execute four commands to compose the data, all of them will be executed in a sequence.
- Threads: In this case, both POSIX threads (pthreads) and green threads can be used. Controlling threads is often not simple for developers, and if a thread fails, data orchestration could be compromised. However, it is the most practical way because there is no need for any external components of the programming language to be used for creating some level of competition or parallelism in the execution of the commands.
- Message Broker: The use of a transactional message broker for transmitting sensitive data within a microservice is quite usual. The disadvantage is the addition of a physical component within a microservice. However, the advantage is the ability to execute strategies that offer more resiliency in data transmission. The simple fact of working with transactions is already a great resource.

Pros:

- The flexibility of implementation
- Independent scalability
- Encapsulation of access to microservices
- Compositional ability and orchestration

Cons:

- The possibility of latency points
- The difficulty in understanding data ownership
- The difficulty of debugging

## Asynchronous messaing design pattern

Pros:

- Independent scalability
- Extreme scalability
- Lazy processing
- Encapsulation of accesses to microservices

Cons:

- Complexity in the monitoring of requisitions
- Complexity of the initial understanding of the pattern
- Difficulty of debugging

## replese piplelines

Build -> Unit Test -> Integration Test -> End-to-End test -> realse

## Monitoring a single service

- `Active Monitoring`is when the server that is to be monitored sends the status information to the monitoring tool
- `Passive Monitoring` is when the monitoring tool requests information about the state of the machine or application from the server

## Multiple service instances per host

**Pros**
One of the key benefits is that it uses resources efficiently. Multiple instances of the service share the server and its operating system. Another benefit of this pattern is the relatively rapid deployment of a microservice instance.

**Cons**
A major disadvantage is that there is little to no isolation of instances of the service unless each service instance is a separate process. If the instances are not separated in different processes, an instance with errors could compromise the entire process, besides making the individual monitoring of instances impossible.

## Service instance per host

- Service instance per VM: Each instance of a microservice is a VM, and the execution of this VM enables the microservice to work.

  - Pros: One of the main benefits of VMs is that each instance of microservice runs in a completely isolated manner, having fixed CPU and memory consumption, without competing for resource consumption with other microservices. Another great benefit of this pattern is the encapsulation of the technology used in the development of microservices.
  - Cons: Each service instance has the overhead of a full VM, including the operating system. Another disadvantage of this approach is that deploying and booting a new version of a microservice is often slow because the cost of booting operating systems using VMs can be high.

- Service instance per container: each microservice instance runs in a container unique to the instance.
  - Pros: They isolate their instances of microservices from one another. Containers are easily monitored. In addition, similar to the VMs, the containers encapsulate the technology used to implement microservices. Unlike the virtual machines, the containers are lighter. The container images are usually very fast to build and initialize.
  - Cons: There are some disadvantages to using containers. Containers are not as secure as VMs because they share the host operating system kernel with each other.Another disadvantage of the containers is the complexity of the infrastructure if they are not using any cloud platform that offers interesting mechanisms to manipulate the containers.