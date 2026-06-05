<img src="https://raw.githubusercontent.com/nov05/pictures/refs/heads/master/interview-questions/20260605_microservice.jpg" width=800>  

<br>  

**ChatGPT:**  

Q1: How do you define service boundaries using Domain-Driven Design?

A: I start with DDD bounded contexts. Each microservice should own a distinct business capability, data model, and lifecycle. I look for areas where the business language, rules, and ownership differ. Services should have high internal cohesion and low coupling with other services. If two domains evolve independently, they are usually good candidates for separate services.

Q2: What criteria determines when to split a service vs. keep it shared?

A: I split a service when teams need independent deployments, scaling characteristics differ, business domains are distinct, or changes in one area frequently affect another. I keep functionality together when it shares the same domain model, changes together, requires strong consistency, or splitting would create excessive communication and operational overhead.

Q3: How do you identify and prevent a "Distributed Monolith"?

A: Warning signs include synchronous call chains everywhere, shared databases, coordinated deployments, and services that cannot function independently. To prevent it, I enforce clear ownership boundaries, database-per-service, asynchronous communication where appropriate, independent deployments, and APIs designed around business capabilities rather than database structures.

Q4: What are the trade-offs between Orchestration and Choreography?

A: Orchestration provides centralized workflow control, visibility, and easier debugging but can create a central dependency and bottleneck. Choreography promotes loose coupling and autonomy through events but can become difficult to understand and troubleshoot as workflows grow. I use orchestration for complex business processes and choreography for simpler event-driven interactions.

Q5: How do you approach the "Strangler Fig" pattern for legacy migration?

A: I place a routing layer in front of the legacy system, then incrementally move functionality into new microservices. Requests for migrated features are routed to the new services while the remaining traffic stays with the monolith. This reduces risk, enables gradual validation, and avoids large-scale rewrites.

Q6: When is a shared library better than a sidecar pattern?

A: A shared library works well for lightweight, language-specific concerns such as validation, logging wrappers, or common utilities. A sidecar is better when functionality must be standardized across languages or independently updated, such as service mesh proxies, authentication agents, or observability components.

Q7: When would you choose gRPC over REST for internal communication?

A: I choose gRPC when low latency, high throughput, strong contracts, streaming, and efficient binary serialization are important. It is particularly useful for service-to-service communication inside a controlled environment. REST remains preferable for public APIs, browser compatibility, and broad ecosystem support.

Q8: How do you implement the Circuit Breaker pattern to prevent cascading failures?

A: I configure thresholds for failure rates, timeouts, and response latency. When failures exceed the threshold, the circuit opens and immediately rejects requests instead of continuing to call the failing dependency. After a cooldown period, the circuit enters a half-open state to test recovery. This protects the system from resource exhaustion and cascading failures.

Q9: How do you handle "thundering herds" when a service comes back online?

A: I use exponential backoff with jitter, rate limiting, connection throttling, load shedding, and gradual traffic ramp-up. Caching and request coalescing also help. The goal is to prevent all clients from retrying simultaneously and overwhelming the recovering service.

Q10: What is your strategy for Retry with Exponential Backoff?

A: I retry only transient failures, not business errors. Each retry waits progressively longer, usually doubling the delay while adding random jitter. I enforce retry limits and overall timeouts to avoid infinite retries and excessive resource consumption.

Q11: How do you ensure API backward compatibility during schema evolution?

A: I make additive changes whenever possible, such as adding optional fields instead of modifying existing ones. Deprecated fields remain available until consumers migrate. API versioning, consumer contract testing, schema validation, and clear deprecation policies help maintain compatibility.

Q12: How do you design for idempotency in message consumers?

A: I assign unique message identifiers and track processed messages in persistent storage. Before processing, the consumer checks whether the message has already been handled. Operations are designed so repeated processing produces the same outcome, preventing duplicates caused by retries.

Q13: How do you choose between Choreography-based vs. Orchestration-based Sagas?

A: I use choreography when workflows are simple and services can react independently through events. I use orchestration when workflows involve many steps, compensation logic, or require centralized visibility and control. Complexity and operational observability are usually the deciding factors.

Q14: How do you handle cross-service joins for complex reporting?

A: I avoid runtime joins across operational services. Instead, I build read models, data warehouses, CQRS projections, or analytics pipelines that aggregate data from multiple services. This keeps operational services independent while supporting reporting requirements efficiently.

Q15: What is the trade-off between Database-per-Service vs. Database-Schema-per-Service?

A: Database-per-Service provides the strongest isolation, independent scaling, and ownership but increases operational overhead. Database-Schema-per-Service is easier to manage operationally but offers weaker isolation and increases the risk of accidental cross-service dependencies.

Q16: How do you manage distributed transactions without Two-Phase Commit (2PC)?

A: I use eventual consistency patterns such as Sagas, compensating transactions, idempotent operations, and reliable messaging. Rather than enforcing global atomicity, each service commits locally and coordination occurs through events and recovery workflows.

Q17: How do you handle "dual write" problems when updating DB and publishing events?

A: I use the Transactional Outbox pattern. The business update and event record are written in the same database transaction. A separate process publishes events from the outbox reliably. This avoids inconsistencies between database state and message delivery.

Q18: What strategies do you use for Eventual Consistency?

A: I use asynchronous messaging, Sagas, retry mechanisms, idempotent consumers, reconciliation jobs, compensating actions, and clear user communication about pending states. Monitoring and observability are essential for detecting and resolving consistency issues.

Q19: How do you implement Distributed Tracing across multiple services?

A: I propagate trace and span identifiers across service boundaries using standards such as OpenTelemetry and W3C Trace Context. Each service records spans for incoming and outgoing operations, allowing end-to-end visibility of request flows, latency, and failures.

Q20: How do you manage configuration drift across multiple environments?

A: I treat configuration as code, store it in version control, automate deployments, and use GitOps practices. Environment-specific values are managed through centralized configuration systems and validated through automated checks to ensure consistency.

Q21: What is your strategy for Dead Letter Queues (DLQ) and message replay?

A: Failed messages are moved to a DLQ after configured retry attempts. I capture failure reasons, provide monitoring and alerting, and build safe replay tools that allow messages to be reprocessed after the root cause is fixed. Replay operations must be idempotent.

Q22: How do you secure service-to-service communication?

A: I use mutual TLS for authentication and encryption, short-lived service identities, strong authorization policies, secret management systems, network segmentation, and regular certificate rotation. Service meshes can simplify enforcement of these controls.

Q23: How do you distinguish between network latency and application slowness?

A: I use distributed tracing, metrics, and profiling. Network latency appears in connection setup, DNS resolution, or request transit times, while application slowness appears in processing spans, database queries, lock contention, or CPU usage. End-to-end tracing helps isolate the exact bottleneck.

Q24: How do you test a microservice in isolation without mocking the entire set of services?

A: I use contract testing, test containers, local dependencies, and lightweight simulators for external services. Consumer-driven contracts verify integrations without requiring all services to run simultaneously. Integration tests focus on real dependencies where practical while keeping the test environment manageable.
