# Microservice notes

## What are microservices?

### Principles and characteristics

1. No monolithic modules
2. Dumb communication pipes
3. Decentralization or self-governance
4. Service contracts and statelessness
5. Lightweith
6. Polylog

### Good parts of microservices

1. self-dependent teams
2. Graceful degration of services
3. Supports polygolt architecture and DevOps
4. Event-driven architecture

### Bad and challenge parts

1. Organization and orchestration
2. Platform
3. Testing
4. Service discovery

### Twelve-factor application

1. **Codebase**: We maintain a single code base here for each microservice, with a configuration specific to their own environments, such as development, QA, and prod. Each microservice would have its own repository in a version control system such as Git, mercurial, and so on.
2. **Dependencies**: All microservices will have their dependencies as part of the application bundle. In Node.js, there is package.json, which mentions all the development dependencies and overall dependencies. We can even have a private repository from where dependencies will be pulled.
3. **Configs**: All configurations should be externalized, based on the server environment. There should be a separation of config from code. You can set environment variables in Node.js or use Docker compose to define other variables.
4. **Backing services**: Any service consumed over the network such as database, I/O operations, messaging queries, SMTP, the cache will be exposed as microservices and using Docker compose and be independent of the application.
5. **Build, release, and run**: We will use automated tools like Docker and Git in distributed systems. Using Docker we can isolate all the three phases using its push, pull, and run commands.
6. **Processes**: Microservices designed would be stateless and would share nothing, hence enabling zero fault tolerance and easy scaling. Volumes will be used to persist data thus avoiding data loss.
7. **Port binding**: Microservices should be autonomous and self-contained. Microservices should embed service listeners as part of service itself. For example— in Node.js application using HTTP module, service network exposing services for handling ports for all processes.
8. **Concurrency**: Microservices will be scaled out via replication. Microservices are scaled out rather than scaled up. Microservices can be scaled or shrunk based on the flow of workload diversity. Concurrency will be dynamically maintained.
9. **Disposability**: To maximize the robustness of application with fast startup and graceful shutdown. Various options include restart policies, orchestration using Docker swarm, reverse proxy, and load balancing with service containers.
10. **Dev/prod parity**: Keep development/production/staging environments exactly alike. Using containerized microservices helps via build once, run anywhere strategy. The same image is deployed across various DevOps stage.
11. **Logs**: Creating separate microservice for logs for making it centralized, to treat as event streams and send it to frameworks such as elastic stack (ELK).
12. **Admin processes**: Admin or any management tasks should be packed as one of the processes, so they can be easily executed, monitored, and managed. This will include tasks like database migrations, one-time scripts, fixing bad data, and so on.

### Communication between microservices

#### Remote Procedure Invocation (PRI) - Apache Thrift and Google's gRPC

RPI uses binary rather than text to keep the payload very compact and efficient. These requests are multiplexed over a single TCP connection, which can allow multiple concurrent messages to be in flight without having to compromise for network consumption usage.

Pros:

1. Request and reply are easy
2. Simple to maintain as there is no middle broker
3. Bidirectional streams with HTTP/2-based transportation methods
4. Efficiently connecting polyglot services in microservices styled architectural ecosystems

Cons:

1. The caller needs to know the locations of service instances, that is, maintain a client-side registry and server-side registry
2. It only supports the request and reply model and has no support for other patterns such as notifications, async responses, the publish/subscribe pattern, publish async responses, streams, and so on

#### Messaging and message bus

Route messages coming from various clients to different microservices destinations. Changes messages to desired transformations as per need. Ability to do message aggregations, segregate a message into multiple messages, and send them to the destination as per need and recompose them. Respond to errors or events. Provide content and routing using the publish-subscribe pattern.

Pros:

1. The client is decoupled from the services; they don't need to discover any services. Loosely coupled architecture throughout.
2. Highly available as the message broker persists messages until the consumer is able to process them for operations.
3. It has support for a variety of communication patterns, including the widely used request/reply, notifications, async responses, publish-subscribe, and so on.

#### Protobufs

**Protocol buffers** or **protobufs** are a binary format created by Google.Some demonstrations effectively show that protobufs is six times faster than JSON.

> Pros:
>
> - Formats for protobufs are self-explaining—formal formats.
> - It has RPC support; you can declare server RPC interfaces as part of protocol files.
> - It has an option for structure validation. As it has larger datatype messages that are serialized on protobufs, it can be validated automatically by the code that is responsible for exchanging them.

> Cons:
>
> - It is an upcoming pattern; hence you won't find many resources or detailed documentation for implementation of protobuf. If you just look for the protobuf tag on Stack Overflow, you will merely see a mere 10,000 questions.
> - As it's binary format, it's non-readable when compared to JSON, which is simple to read and analyze on the other hand. The next generation of protobuf and flatbuffer is already available now.

### Service discovery

#### Service registry for service-service communication

1. `etcd`: A key-value store used for shared configuration and service discovery. Projects such as Kubernates and cloud foundry are based on etcd as it can be highly available, key-value based, and consistent.
2. `consul`: Yet another tool for service discovery. It has wide options such as exposed API endpoints that allow the client to register and discover services and perform health checks to determine service availability.
3. `ZooKeeper`: Very widely used, highly available, and a high performant coordinated service used in distributed applications. Originally a subproject of Hadoop, Zookeeper is a widely used top-level project and it comes preconfigured with various frameworks.

#### Server-side discovery

All requests made to any of the services are routed via a router or load balancers that run in a location known to client interfaces. The router then queries a maintained registry and forwards the request based on the query response

Pros:

1. The client does not need to know the location of different microservices. They just need to know the location of the router and the service discovery logic is completely abstracted from the client so there is zero logic at the client end.
2. Some environments provide this component functionality for free.

Cons:

1. It has more network hops, that is, one from the client service registry and another from the service registry microservice.
2. If the load balancer is not provided by the environment, then it has to be set up and managed. If not properly handled, then it can be a single point of failure.
3. The selected router or load balancer must support different communication protocols for modes of communication.

#### Client-side discovery

Under this mode of discovery, the client is responsible for handling the network location of available microservices and load balancing incoming requests across them. The client needs to query a service registry (a database of available services maintained on the client side). The client then selects service instances on the basis of an algorithm and then makes a request
Netflix uses this pattern extensively and has open sourced their tools **Netflix OSS, Netflix Eureka, Netflix Ribbon, and Netflix Prana**. Using this pattern has the following advantages:

1. High performance and availability as there are fewer transition hops, that is, the client just has to invoke the registry and the registry will redirect to the microservice as per their needs.
2. This pattern is fairly simple and highly resilient as besides the service registry there are no moving parts. As the client knows about available microservices, they can make intelligent decisions easily such as to use a hash, when to trigger auto-scaling, and so on.
3. **One significant drawback** of using this mode of service discovery is implementation of client-side service discovery logic has to be done in every programming language of the framework that is used by the service clients. For example, Java, JavaScript, Scala, Node.js, Ruby, and so on.

#### Registration partterns

While using this pattern, any microservice instance is responsible for registering and deregistering itself from the maintained service registry. To maintain health checks, a service instance sends heartbeat requests to prevent its registry from expiring.

Pros:

- It couples the service tightly to the self-service registry, which forces us to enable the service registration code in each language we are using in the framework
- Any microservice that is in running mode, but is not able to handle requests, will often be unaware of which state to pursue, and will often end up forgetting to unregister from the registry

### Data management

#### Database per service

- **Private tables/collections per service**: Each microservice has a set of defined tables or collections that can only be accessed by that service
- **Schema per service**: Each service has a schema that can only be accessed via the microservice it is bound to
- **Database per service**: Each microservice maintains its own database as per its needs and requirements

Pros:

- Loosely coupled services that can stand on their own; changes to one service's datastore won't affect any other services.
  Each service has the liberty to select the datastore as required.
- Each microservice has the option of whether to go for relational or non-relational databases as per need. For example, any service that needs intensive search results on text may go for **Solr** or **Elasticsearch**, whereas any service where there is structured data may go for any SQL database.

Cons:

- Handling complex scenarios that involve transactions spanning across multiple services. The CAP theorem states that it is impossible to have more than two out of the following three guarantees—consistency, availability, and partitions in the distributed datastore, so transactions are generally avoided.
- Queries ranging across multiple databases are challenging and resource consuming.
- The complexity of managing multiple SQL and non-SQL datastores.

To overcome the drawbacks, the following patterns are used while maintaining a database per service:

- **Sagas**: A saga is defined as a batch sequence of local transactions. Each entry in the batch updates the specified database and moves on by publishing a message or triggering an event for the next entry in the batch to happen. If any entry in the batch fails locally or any business rule is violated, then the saga executes a series of compensating transactions that compensate or undo the changes that were made by the saga batch updates.
- **API Composition:** This pattern insists that the application should perform the join rather than the database. As an example, a service is dedicated to query composition. So, if we want to fetch monthly product distributions, then we first retrieve the products from the product service and then query the distribution service to return the distribution information of the retrieved products.
- **Command Query Responsibility Segregation (CQRS)**: The principle of this pattern is to have one or more evolving views, which usually have data coming from various services. Fundamentally, it splits the application into two parts—the command or the operating side and the query or the executor side. It is more of a publisher-subscriber pattern where the command side operates create/update/delete requests and emits events whenever the data changes. The executor side listens for those events and handles those queries by maintaining views that are kept up to date, based on the subscription of events that are emitted by the command or operating side.

### Sharing concerns

- Externalized configuration
- Observability
  - Log aggregation
  - Distributed tracing

### Design patterns

#### Asynchronous messaging microservice design patterns

To successfully implement this design, the following aspects should be considered:

- When you need high scalability, or your current domain is already a message-based domain, then preference should be given to message-based commands over HTTP.
- Publishing events across microservices, as well as changing the state in the original microservices.
- Make sure that events are communicated across; mimicking the event would be a very bad design pattern.
- Maintain the position of the subscriber's consumer to scale up performance.
  When to make a rest call and when to use a messaging call. As HTTP is a synchronous call, it should be used only when needed.

When to use:

- When you want to use real-time streaming, use the Event Firehouse pattern, which has KAFKA as one of its key components.
- When your complex system is orchestrated in various services, one of the variants of this system, RabbitMQ, is extremely helpful.
- Often, instead of subscribing to services, directly subscribing to the datastore is advantageous. In such a case use, GemFire or Apache GeoCode following this pattern is helpful

When not to use:

- When you have heavy database operations during event transmission, as database calls are synchronous
- When your services are coupled
- When you don't have standard ways defined to handle data conflict situations

#### backend for frontends

Thins to consider:

- A fair consideration of the amount of BFFs to be maintained. A new BFF should only be created when concerns across a generally available service can be separated out for a specific interface.
- A BFF should only contain client/interface-specific code to avoid code duplication.
- Divide responsibilities across teams for maintaining BFFs.
- This should not be confused with a **Shim**, a converter to the convert to interface-specific format required for that type of interface.

When to use:

- There are varying differences in a general-purpose backend service across multiple interfaces and there are multiple updates at any point in time in a single interface.
- You want to optimize a single interface and not disturb the utility across other interfaces.
- There are various teams, and implement an alternative language for a specific interface and you want to maintain it separately.

When not to use:

- Do not use this pattern to handle generic parameter concerns such as authentication, security, or authorization. This would just increase latency.
- If the cost of deploying an extra service is too high.
- When interfaces make the same requests and there is not much difference between them.
- When there is only one interface and support for multiple interfaces is not there, a BFF won't make much sense.

#### Gateway aggregation and offloading

**Gateway aggregator**: It receives the client request, then it decides to which different systems it has to dispatch the client request, gets the results, and then aggregates and sends them back to the client. For the client, it is just one request. Overall round trips between client and server are reduced.

Things to consider:

- Do not introduce service coupling, that is, the gateway can exist independently, without other service consumers or service implementers.
- Here, every microservice will be dependent on the gateway. Hence, the network latency should be as low as possible.
- Make sure to have multiple instances of the gateway, as only a single instance of the gateway may introduce it as a single point of failure.
- Each of the requests goes through the gateway. Hence, it should be ensured that gateway has efficient memory and adequate performance, and can be easily scaled to handle the load. Have one round of load testing to make sure that it is able to handle bulk load.
- Introduce other design patterns such as bulkheads, retry, throttle, and timeout for efficient design.
- The gateway should handle logic such as the number of retries, waiting for service until.
- The cache layer should be handled, which can improve performance.
- The gateway aggregator should be behind the gateway, as the request aggregator will have another. Combining them in a gateway will likely impact the gateway and its functionalities.
- While using the asynchronous approach, you will find yourself smacked by too many promises of callback hell. Go with the reactive approach, a more declarative style. Reactive programming is prevalent from Java to Node.js to Android. You can check out this link for reactive extensions across different links: https://github.com/reactivex.
- Business logic should not be there in the gateway.

When to use:

- There are multiple microservices across and a client needs to communicate with multiple microservices.
- Want to reduce the frequent network calls when the client is in lesser range network or cellular network. Breaking it in one request is efficient as then the frontend or the gateway will only have to cache one request.
- When you want to encapsulate the internal structure or introduce an abstract layer to a large team present in your organization.

When not to use:

- When you just want to reduce the network calls. You cannot introduce a whole level of complexity for just that need.
- The latency by the gateway is too much.
- You don't have asynchronous options in the gateway. Your system makes too many synchronous calls for operations in the gateway. That would result in a blocking system.
- Your application can't get rid of coupled services.

#### Proxy routing and throttling

When you have multiple microservices that you want to expose across a single endpoint and that single endpoint routes to service as per need. This application is helpful when you need to handle imminent transient failures and have a retry loop on a failed operation, thus improve the stability of the application. This pattern is also helpful when you want to handle the consumption of resources used by a microservice.

This pattern is used to meet the agreed SLAs and handle loads on resources and resource allocation consumption even when an increase in demand places loads on resources:

Things to consider:

- The gateway can be a single point of failure. Proper steps have to be taken to ensure that it has fault tolerant capabilities during development. Also, it should be run in multiple instances.
- Gateway should have proper memory and resource allocation otherwise it will introduce a bottleneck. Proper load testing should be done to ensure that failures are not cascaded.
- Routing can be done based on IP, header, port, URL, request parameter, and so on.
- The retry policy should be crafted very carefully based on the business requirements. It's okay in some places to have a please try again rather than having waiting periods and retrials. The retry policy may also affect the responsiveness of the application.
- For effective application, this pattern should be combined with **Circuit Breaker Application**.
- If service is idempotent, then and only then should it be retried. Trying retrial on other services may have unhealthy consequences. For example, if there is a payment service that waits for responses from other payment gateways, the retry component may think it fails and may send another request and the customer gets charged twice.
- Different exceptions should handle the retry logic accordingly, based on the exceptions.
- Retry logic should not disturb transaction management. The retry policy should be used accordingly.
- All failures that trigger a retry should be logged and handled properly for future scenarios.
- An important point to be considered is this is no replacement for exception handling. The first priority should be given to exceptions always, as they would not introduce an extra layer and add latency.
- Throttling should be added early in the system as it's difficult to add once the system is implemented; it should be carefully designed.
- Throttling should be performed quickly. It should be smart enough to detect an increase in activity and react accordingly by taking appropriate measures.
- Consideration between throttling and auto-scaling should be decided based on business requirements.
  The requests that are throttled should be effectively placed in a queue based on priority.

When to use:

- To ensure that agreed LSAs are maintained.
- To avoid a single microservice consuming the majority of the pool of resources and avoid resource depletion by a single microservice.
- To handle sudden bursts in consumption of microservices.
- To handle transient and short-lived faults.

When not to use:

- Throttling shouldn't be used as a means to handle exceptions.
- When faults are long-lasting. If this pattern is applied in that case, it will severely affect the performance and responsiveness of the application.

#### Ambassador and sidecar pattern

This pattern is used when we want to segregate common connectivity features such as monitoring, logging, routing, security, authentication, authorization, and more. It creates helper services that act as ambassadors and sidecars that do the objective of sending requests on behalf of a service. It is just another proxy that is located outside of the process. Specialized teams can work on it and let other people not worry about it so as to provide encapsulation and isolation. It also allows the application to be composed of multiple frameworks and technologies.

Things to consider:

- The ambassador can introduce some latency. Deep thought should be given on whether to use a proxy or expose common functionalities as the library.
- Adding generalized functionalities in ambassador and sidecar is beneficial, but is it required for all scenarios? For example, consider the number of retries to a service, it might not be common for all use cases.
- The language or framework in which ambassador and sidecar will be built, managed, and deployed strategy for it. The decision to create single or multiple instances of it based on need.
- Flexibility to pass some parameters from service to ambassador and proxy and vice versa.
- The deployment pattern: this is well suited when the ambassador and sidecar are deployed in containers.
- The inter-micro service communication pattern should be such that it is framework agnostic or language agnostic. This would be beneficial in the long run.

When to use:

- When there are multiple frameworks and languages involved and you need a common set of general features such as client connectivity, logging, and so on throughout the application. An ambassador and sidecar can be consumed by any service across the application.
- Services are owned by different teams or different organizations.
- You need independent services for handling this cross-cutting functionality and they can be maintained independently.
- When your team is huge and you want specialized teams to handle, manage, and maintain core cross-cutting functionalities.
- You need to support the latest connectivity options in a legacy application or an application that is difficult to change.
- You want to monitor resource consumption across the application and cut off a microservice if its resource consumption is huge.-

When not to use

- When network latency is utmost. Introducing a proxy layer would introduce some overhead that will create a slight delay, which may not be good for real-time scenarios.
- When connectivity features cannot be generalized and require another level of integration and dependency with another service.
- When creating a client library and distributing it to the microservices development team as a package.
- For small applications where introducing an extra layer is actually an overhead.
- When some services need to scale independently; if so, then the better alternative would be to deploy it separately and independently.

#### Anti-corruption microservice design pattern

This design provides an easy solution for this by introducing a facade between modern and legacy applications. This design pattern ensures that the design of an application is not hampered or blocked by legacy system dependencies:

Things to consider:

- The ACL should be properly scaled and given a better resources pool, as it will add latency to calls made between two systems.
- Make sure that the corruption layer you introduce is actually an improvement and you don't introduce yet another layer of corruption.
- The ACL adds an extra service; hence it must be effectively managed and maintained and scaled.
- Effectively decide the number of ACLs. There can be many reasons to introduce an ACL—a means to translate undesirable formats of the object in required formats means to communicate between different languages, and so on.
- Effective measures to make sure that transactions and data consistency are maintained between both systems and can be monitored.
- The duration of the ACL, will it be permanent, how will the communication be handled.
- While an ACL should successfully handle exceptions from the corruption layer, it should not completely, otherwise it would be very difficult to preserve any information about the original error.

When to use:

- There is a huge system up for refactoring from monolithic to microservices and there is a phase-by-phase migration planned instead of the big bang migration wherein the legacy system and new system need to coexist and communicate with each other.
- If the system that you are undertaking is dealing with any data source whose model is undesirable or not in sync with the needed model, this pattern can be introduced and it will do the task of translating from undesirable formats to needed formats.
- Whenever there is a need to link two bounded contexts, that is, a system is developed by someone else entirely and there is very little understanding of it, this pattern can be introduced as a link between systems.

When not to use:

- There are no major differences between new and legacy systems. The new system can coexist without the legacy system.
- You have lots of transactional operations and maintaining data consistency between the ACL and the corrupt layer adds too much latency. In such case, this pattern can be merged with other patterns.
- Your organization doesn't have extra teams to maintain and scale the ACL as and when needed.

#### Bulkhead design pattern

Separate out different services in the microservices application into various pools such that if one of the services fails, the others will continue to function irrespective of failure.

Things to consider:

- Define proper independent partitions in the application based on business and technical requirements.
- Bulkheads can be introduced in forms of thread pools and processes. Decide which one is suitable for your application.
- Isolation in the deployment of your microservices.

When to use:

- The application is huge and you want to protect it from cascading or spreading failures
- You can isolate critical services from standard services and you can allocate separate pools for them

When not to use:

- When you don't have that much budget for maintaining separate overheads in terms of cost and management
- The added level of complexity of maintaining separate pools is not necessary
- Your resources usage is unexpected and you can't isolate your tenants and keep a limit on it as it is not acceptable when you would place several tenants in one partition

#### Circuit breaker

Services sometimes need to collaborate with each other when they need to handle requests. In such cases, there is a very high scenario that the other service is not available, is showing high latency, or is unusable. This pattern essentially solves this issue by introducing a breakage in the circuit that stops propagation in the whole architecture:

Things to consider:

1. Since you are invoking a remote call, and there may be many remote call invocation asynchronous and reactive principles for using future, promises, async, and await is must.
2. Maintain a queue of requests; when your queue is overcrowded, you can easily trip the circuit. Always monitor the circuit, as you will often need to activate it again for an efficient system. So, have a mechanism ready for reset and failure handlers.
3. You have a persistent storage and network cache such as **Memcache** or **Redis** to record availability.
4. Logging, exception handling, and relaying failed requests.

When to use:

- When you don't want your resources to be depleted, that is, an action that is doomed to fail shouldn't be tried until it is fixed. You can use it to check the availability of external services.
- When you can compromise a bit on performance, but want to gain high availability of the system and not deplete resources.

When not to use:

- You don't have an efficient cache layer that monitors and maintains states of services for a given time for requests across the clustered nodes.
- For handling in-memory structures or as the substitute for handling exceptions in business logic. This would add overhead to performance.

#### Strangler pattern

This pattern is about eventually migrating a legacy system by incrementally replacing particular parts of functionality with new microservices application and services. It eventually introduces a proxy that redirects either to the legacy or the new microservices, until the migration is complete and at the end, you can shut off the strangler or the proxy:

- **Reconstruct**: Construct a new application or site (in serverless or AWS cloud-based on modern principles). Incrementally reconstruct the functionalities in an agile manner.
- **Coexist**: Leave the legacy application as it is. Introduce a facade that eventually acts as a proxy and decides where to route the request based on the current migration status. This facade can be introduced at web server level or programming level based on various parameters such as IP address, user agent, or cookies.
- **Terminate**: Redirect everything to the modern migrated application and loosen off all the ties with the legacy application.

Things to consider:

- The facade or the proxy needs to be updated with the migration.
- The facade or the proxy shouldn't be a single point of failure or bottleneck.
- When the migration is complete, facade will evolve as the adapter for legacy applications.
- The new code written should be such that it can easily be intercepted, so in future, we can replace it in future migrations.

When to use:

- When you want to follow the test-driven on behavior-driven development, and run fast and comprehensive tests with the accessibility of code coverages and adapt CI/CD.
- Your application can be contained bounded contexts within which a model applies in the region. As an example, in a shopping cart application, the product module would be one context.

When not to use:

- When you are not able to intercept the user agent request, or you are not able to introduce a facade in your architecture.
- When you think of doing a page by page migration at a time or you are thinking of doing it all at once.
- When your application is more frontend-driven; that's where you have to entirely change and rework the interacting framework based on the way the frontend is interacting with services, as you don't want to expose the various ways the user agent is interacting with services.
