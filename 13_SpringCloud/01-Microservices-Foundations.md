# Microservices Architecture Notes for Interviews

> Concise, interview-focused notes on microservices architecture for backend and full stack engineers.

---

## Table of Contents

1. [What is Microservices](#1-what-is-microservices)
2. [Core Characteristics of Microservices](#2-core-characteristics-of-microservices)
3. [Benefits of Microservices](#3-benefits-of-microservices)
4. [Challenges of Microservices](#4-challenges-of-microservices)
5. [Microservices vs Monolith](#5-microservices-vs-monolith)
6. [When Microservices is Better and When Monolith is Better](#6-when-microservices-is-better-and-when-monolith-is-better)
7. [Microservices with Shared Database](#7-microservices-with-shared-database)
8. [Important Interview Keywords](#8-important-interview-keywords)
9. [Most Asked Interview Questions](#9-most-asked-interview-questions)
10. [Common Beginner Mistakes](#10-common-beginner-mistakes)
11. [Key Takeaways](#11-key-takeaways)
12. [Final Summary](#12-final-summary)

---

# 1. What is Microservices

Microservices is an architectural style where an application is built as a set of **small, focused, loosely coupled services**, and each service usually owns a **specific business capability**.

Each service typically:
- Solves one business problem well
- Has its own business logic
- Can be deployed independently
- Often owns its own database
- Communicates with other services over the network using HTTP, gRPC, messaging, or events

## Why microservices were introduced

Microservices became popular because large monolithic systems often become difficult to:
- Change safely
- Deploy frequently
- Scale efficiently
- Understand across many teams
- Own clearly when the business becomes large

## Problems with monolithic systems

| Problem | Meaning | Impact |
|---|---|---|
| Large codebase | Everything lives in one application | Hard onboarding and slow development |
| Tight coupling | Modules depend heavily on each other | One change can break unrelated areas |
| Full redeploy | Small change requires redeploying the whole app | Slower releases |
| Full scaling | One hot feature forces full app scaling | Wasted infrastructure cost |
| Shared risk | Bug in one area can impact the whole app | Bigger blast radius |
| Team coordination overhead | Many developers work in same codebase | Merge conflicts and release delays |

## Core idea behind microservices

Microservices are not just “many small APIs.” They are a **distributed systems architecture**.

That means:
- Services run separately
- Calls happen over the network
- Latency and timeouts matter
- Partial failures happen
- Data is distributed
- Monitoring, tracing, deployment automation, and resilience become essential

## Business capability concept

A service should ideally be organized around a **business capability**, not a technical layer.

**Good boundaries**:
- `Order Service`
- `Payment Service`
- `Inventory Service`
- `Notification Service`

**Bad boundaries**:
- `Controller Service`
- `Validation Service`
- `Database Access Service`

Business capability boundaries usually give better ownership and lower coupling.

## Independent deployability

Independent deployability means one service can be changed and released without redeploying the entire system.

Example:
- Updating `Payment Service` should not require redeploying `Catalog Service`
- Updating pricing logic at Uber should not force redeploying notifications or login services

This is one of the biggest reasons companies adopt microservices.

## Loose coupling and high cohesion

- **Loose coupling** means services should depend only on other services' public contracts, not their internal code or database schema
- **High cohesion** means everything inside a service should belong to one clear responsibility

Example:
- `Payment Service` should handle payment authorization, capture, refunds, and status
- It should not also handle product catalog or delivery routing

## SOA vs Microservices

| Aspect | SOA | Microservices |
|---|---|---|
| Service size | Often larger enterprise services | Smaller, more focused services |
| Governance | More centralized | More decentralized |
| Communication | Often ESB-heavy | Lightweight APIs/events are common |
| Deployment | Often less independent | Strong focus on independent deployment |
| Data ownership | Shared enterprise data is common | Database per service is preferred |
| Main goal | Enterprise integration | Speed, autonomy, and scalable delivery |

## Architecture overview

```text
                    +-------------------+
                    |   Web / Mobile    |
                    |      Clients      |
                    +---------+---------+
                              |
                              v
                    +-------------------+
                    |    API Gateway    |
                    +---------+---------+
                              |
      -------------------------------------------------------------
      |              |               |              |              |
      v              v               v              v              v
+-----------+  +-----------+  +-------------+  +-----------+  +-------------+
| User      |  | Product   |  | Order       |  | Payment   |  | Notification|
| Service   |  | Service   |  | Service     |  | Service   |  | Service     |
+-----+-----+  +-----+-----+  +------+------+  +-----+-----+  +------+------+
      |              |               |                |                |
      v              v               v                v                v
+-----------+  +-----------+  +-------------+  +-----------+  +-------------+
| User DB   |  | ProductDB |  | Order DB    |  | PaymentDB |  | Message DB  |
+-----------+  +-----------+  +-------------+  +-----------+  +-------------+
```

### Why API Gateway is used
- Single entry point for clients
- Request routing
- Authentication and authorization
- Rate limiting
- Response aggregation
- Hiding internal service structure from clients

## Internal request flow example

```text
Client -> API Gateway -> Order Service
                      -> Inventory Service
                      -> Payment Service
                      -> Order DB
                      -> Publish OrderCreated event
                      -> Notification / Shipping / Analytics
```

Even a simple action like “Place Order” can involve multiple services.

## Service-to-service communication

### Synchronous communication
- HTTP/REST
- gRPC
- Good when immediate response is needed

### Asynchronous communication
- Kafka
- RabbitMQ
- Event-driven messaging
- Good for decoupling and background processing

## Real-world examples

### Netflix
Different services may handle:
- Profile
- Recommendations
- Billing
- Viewing history
- Content metadata

### Amazon
Different services may handle:
- Catalog
- Cart
- Pricing
- Orders
- Payments
- Inventory
- Shipping

### Uber
Different services may handle:
- Ride booking
- Driver allocation
- Pricing
- Maps
- Payments
- Notifications

## Important interview points

- Microservices are about **independent ownership**, not just separate processes
- “Micro” does **not** mean tiny
- Microservices are useful when scale, team size, and domain complexity grow
- They are distributed systems, so network and failure handling matter a lot

---

# 2. Core Characteristics of Microservices

A microservices system is usually identified by a few core characteristics.

## Core characteristics table

| Characteristic | Meaning | Why it matters | If ignored |
|---|---|---|---|
| Loose Coupling | Services depend minimally on each other | Easier change and release independence | One change breaks many services |
| High Cohesion | One service owns related functionality | Clear boundaries and easier ownership | Confused design and overlap |
| Independent Deployment | Service can be released on its own | Faster delivery and smaller blast radius | Synchronized releases |
| Business Capability Ownership | Service matches real business function | Better team and domain ownership | Technical fragmentation |
| Decentralized Data Management | Each service owns its data | Prevents schema coupling | Shared DB causes coordination problems |
| Scalability | Services can scale independently | Better resource usage | Full app scaling waste |
| Fault Isolation | One service failure should stay local | Better resilience | Cascading failures |
| Technology Diversity | Different services can use different stacks when needed | Right tool for the problem | Too much diversity causes operational chaos |
| Automation and DevOps | CI/CD, monitoring, deployment automation | Essential at scale | Manual operations become impossible |
| Resilience | System handles faults gracefully | Better uptime | Outages spread quickly |
| Service Independence | Teams can own lifecycle end-to-end | Better accountability and speed | Architecture exists only in name |

## Team ownership

Healthy microservices organizations usually follow:
- One team owns one or more services end-to-end
- Team owns development, testing, deployment, monitoring, and incident response
- This is often summarized as **“you build it, you run it”**

## Conway’s Law

> Systems tend to mirror the communication structure of the organizations that build them.

This is why service boundaries often match team boundaries.

## Bounded context basics

A **bounded context** is a clear domain boundary where a model has a specific meaning.

Example:
- `Order` in `Order Service` may mean customer purchase lifecycle
- `Order` in `Warehouse Service` may mean fulfillment work item
- `Order` in `Billing Service` may mean invoice reference

This is why forcing one universal model across all services often creates problems.

## Good design vs bad design

### Good design
- `Order Service -> Payment API`
- `Payment Service` owns payment DB
- Communication through contracts or events

### Bad design
- `Order Service -> directly reads Payment DB`
- Multiple services write to same tables
- One release requires all services to change together

## Important interview points

- Loose coupling protects independent deployment
- Database-per-service is mainly about autonomy, not fashion
- Service boundaries should come from domain understanding, not random splitting

---

# 3. Benefits of Microservices

Microservices are adopted because they solve real engineering and organizational problems at scale.

## Benefits table

| Benefit | Technical value | Business value | Tradeoff | Real-world example |
|---|---|---|---|---|
| Independent Scaling | Scale only hot services | Better resource efficiency | More infra complexity | Search scales more than payment during sales |
| Faster Deployments | Smaller release unit | Faster time-to-market | Needs strong CI/CD | Recommendation service can ship separately |
| Team Autonomy | Teams work with less interference | Faster delivery | Needs standards and API discipline | Separate teams for pricing, maps, payments |
| Better Maintainability | Smaller codebases per service | Easier onboarding | System-wide complexity increases | New engineer learns one domain first |
| Better Fault Isolation | Failures can be localized | Higher availability | Needs resilience patterns | Recommendations fail but watch flow still works |
| Technology Flexibility | Right tool for each problem | Better problem-solving | More operational overhead | Search, ML, and payments may use different stacks |
| Continuous Delivery Support | Easier safe rollouts | Faster experimentation | Heavy automation investment | Canary/blue-green releases |
| Easier Experimentation | Service-local changes | Faster product iteration | Needs tracking and governance | A/B testing recommendation logic |
| Organizational Scaling | Architecture supports many teams | Business grows faster | Platform maturity required | Amazon/Uber-style domain ownership |

## Why companies adopt microservices

Companies usually adopt microservices when they need:
- Independent deployments
- Independent scaling
- Clear domain ownership
- Faster release cycles
- Better fault isolation
- Better organizational scaling

## Cost reality

Microservices do **not** automatically reduce cost.

They can reduce waste in scaling, but they also increase:
- Infrastructure cost
- Monitoring cost
- CI/CD cost
- Operational overhead
- Platform engineering effort

## Important interview points

- Microservices improve local maintainability but can reduce system-wide simplicity
- Their benefits are strongest in large systems with many teams and uneven workloads
- If the organization is small, the overhead may not be worth it

---

# 4. Challenges of Microservices

This is one of the most important interview sections. Strong candidates explain both benefits and complexity.

## Main challenges

| Challenge | Why it is hard | Example |
|---|---|---|
| Distributed system complexity | Many moving parts instead of one app | One user flow crosses many services |
| Network latency | Every remote call adds delay | Gateway -> Auth -> Order -> Payment |
| Partial failures | One service can fail while others are fine | Order is up but payment is down |
| Service discovery | Service locations may change dynamically | Containers/pods restart and move |
| Distributed transactions | One ACID transaction across services is difficult | Order + inventory + payment |
| Data consistency | Data is spread across multiple databases | Order created but payment failed |
| Eventual consistency | State may update over time, not instantly | Order, notification, analytics update later |
| Monitoring and logging | Logs and metrics are spread out | Need correlation across services |
| Deployment complexity | Many release units and dependencies | Version coordination becomes harder |
| Testing complexity | Integration and contract testing are harder | End-to-end tests become fragile |
| Debugging complexity | Tracing a request across services is hard | Hard to reproduce production failures |
| API versioning | Consumers upgrade at different times | Breaking change can affect many teams |
| Security complexity | More network paths and attack surface | Service-to-service auth is required |
| Configuration management | Many services, many environments | Drift and secret issues |
| Cascading failures | One slow service affects others | Payment slowness breaks checkout |
| Infrastructure overhead | More tooling is required | Containers, orchestration, tracing |

## Why distributed systems are hard

Once services communicate over a network, you must deal with:
- Latency
- Timeouts
- Retries
- Partial failures
- Duplicate events
- Ordering issues
- Eventual consistency
- Observability needs

**Interview line:** The network is not free, not instant, and not perfectly reliable.

## Important patterns

### Circuit Breaker
Stops repeated calls to a failing service so that failures do not spread.

### Retry
Retries transient failures, but should use limits, backoff, and idempotency.

### Timeout
Prevents resources from waiting forever on a dependency.

### Bulkhead
Separates resources so one dependency failure does not consume everything.

### Saga Pattern
Used for multi-service business transactions using local transactions and compensation.

Example:
1. Create order
2. Reserve inventory
3. Authorize payment
4. If payment fails, release inventory and cancel order

### CQRS basics
Separates write and read models when needed for scalability or domain complexity.

### Event-driven architecture basics
Services publish events and other services react asynchronously.

Example:
- `OrderCreated` -> notification, analytics, shipping preparation

## CAP theorem basics

Under a network partition, distributed systems cannot fully guarantee both strong consistency and full availability at the same time.

This is why distributed systems often need practical tradeoffs.

## Synchronous vs asynchronous communication

| Aspect | Synchronous | Asynchronous |
|---|---|---|
| Pattern | Request-response | Events/messages |
| Coupling | More temporal coupling | Lower temporal coupling |
| Response | Immediate | Often delayed/background |
| Debugging | Easier at first | Harder |
| Resilience | More fragile to outages | Better decoupling |
| Example | Get user profile | Publish `OrderCreated` event |

## Practical scenarios

### Payment service failure
- `Order Service` may mark order as `PENDING_PAYMENT`
- Retry later
- Avoid blocking unrelated flows

### Order rollback scenario
- Order created
- Inventory reserved
- Payment fails
- Compensation releases inventory and updates order status

### Non-critical service outage
If recommendation service fails:
- Show fallback content
- Keep purchase or playback flow working

## Tools commonly used

| Tool | Common use |
|---|---|
| Docker | Containerize services |
| Kubernetes | Run and manage containers |
| Kafka | Event streaming |
| RabbitMQ | Queue-based messaging |
| Eureka / Consul / K8s DNS | Service discovery |
| Prometheus | Metrics and monitoring |
| ELK Stack | Centralized logging |
| Zipkin / Jaeger | Distributed tracing |
| Resilience4j | Circuit breaker, retry, timeout, bulkhead |

## Distributed monolith warning

A **distributed monolith** looks like microservices physically, but behaves like a tightly coupled monolith.

Common signs:
- Shared database
- Coordinated deployments
- Direct cross-service DB access
- Strong runtime dependencies
- One change affects many services

## Important interview points

- Microservices fail most often because teams underestimate operational complexity
- Observability is as important as business logic
- The hardest part is usually not coding, but operating the platform reliably

---

# 5. Microservices vs Monolith

Neither architecture is always better. The correct answer depends on context.

## Architecture diagram comparison

### Monolith

```text
Client
  |
  v
+--------------------------------------------+
|                Monolith App                |
|--------------------------------------------|
| User | Catalog | Cart | Order | Payment    |
|--------------------------------------------|
| Shared Code + Shared Deployment + Often DB |
+--------------------------------------------+
  |
  v
+-------------------+
|   Single Database |
+-------------------+
```

### Microservices

```text
Client
  |
  v
+-------------+
| API Gateway |
+------+------+ 
       |
       +-------------------+-------------------+-------------------+
       |                   |                   |                   |
       v                   v                   v                   v
+-------------+      +-------------+     +-------------+     +-------------+
| User Service|      | Order Svc   |     | Payment Svc |     | Catalog Svc |
+------+------+      +------+------+     +------+------+     +------+------+
       |                    |                   |                   |
       v                    v                   v                   v
   User DB              Order DB            Payment DB          Catalog DB
```

## Detailed comparison

| Aspect | Monolith | Microservices |
|---|---|---|
| Architecture | Single deployable application | Multiple services |
| Deployment | Entire app deploys together | Services can deploy independently |
| Scaling | Scale whole app | Scale individual services |
| Team structure | Good for small teams | Better for many domain teams |
| Database | Usually one shared DB | Usually database per service |
| Fault isolation | Lower | Better if designed well |
| Debugging | Easier | Harder |
| Testing | Simpler | More contract/integration work |
| Performance | Faster internal calls | Slower network calls |
| Infrastructure | Lower overhead | Higher overhead |
| Technology choice | More uniform | More flexible |
| Cost | Lower initially | Higher operational cost |
| Maintainability | Good when app is small/modular | Better for large domains with good boundaries |

## Monolith advantages

- Faster to start
- Simpler local development
- Lower infrastructure cost
- Easier debugging
- Easier testing for small teams

## Microservices advantages

- Independent deployment
- Independent scaling
- Better team autonomy
- Better fault isolation
- Better fit for large complex domains

## Modular monolith

A **modular monolith** is a single deployable application with strong internal boundaries.

Why it matters:
- Good starting point for many companies
- Easier than microservices operationally
- Easier to extract services later if needed

## Distributed monolith

A **distributed monolith** is the failure case where services are physically separate but still tightly coupled.

Why it is dangerous:
- You keep monolith coupling
- You add distributed systems complexity
- You get the worst parts of both models

## Important interview points

- Monolith is not bad architecture by default
- Many startups should begin with a modular monolith
- Microservices become useful when scale, team size, and domain complexity justify the overhead

---

# 6. When Microservices is Better and When Monolith is Better

## Microservices are better when

- Many teams need independent ownership
- Different parts of the system scale differently
- The domain is large and naturally split into business capabilities
- Deployment speed matters a lot
- The organization is mature in CI/CD, monitoring, and operations
- Different technical workloads genuinely need different tools

## Monolith is better when

- Team is small
- Product is still early or an MVP
- Domain is not yet stable
- Traffic is moderate
- Operational simplicity matters more than scaling flexibility
- There is no strong DevOps/platform maturity yet

## Real-world scenarios

| Scenario | Better fit | Why |
|---|---|---|
| Early-stage startup | Monolith / modular monolith | Fast iteration, low cost, simpler ops |
| Small internal business app | Monolith | Simpler deployment and maintenance |
| Large e-commerce platform | Microservices | Orders, catalog, payments, search scale differently |
| Large social media platform | Microservices | Feed, messaging, media, search have very different workloads |
| Banking platform | Mixed / selective decomposition | Strong consistency and governance matter a lot |

## Decision framework

Ask these questions:
- Is the product proven?
- Is scale a real problem yet?
- Are domains clear enough to separate?
- Do many teams need independent ownership?
- Do we have CI/CD, observability, and incident maturity?
- Can we handle distributed data consistency and debugging?

If most answers are **no**, a monolith is usually the better choice.

## Migration strategy from monolith to microservices

A practical migration path:
1. Start with monolith or modular monolith
2. Identify pain points and boundaries
3. Extract low-risk services first
4. Add APIs/events
5. Move data ownership carefully
6. Improve observability and deployment maturity
7. Continue gradually

Good first candidates:
- Notification service
- Search service
- Reporting or analytics service

Hard early candidates:
- Core order/payment flow with complex transactions

## Important interview points

- Premature microservices often fail
- Migration should usually be gradual, not big-bang
- Architecture should follow business need, not trend

---

# 7. Microservices with Shared Database

A common transitional pattern is splitting code into services while still keeping one shared database.

## What it is

Multiple services use the same database, and sometimes even the same tables.

```text
                +-------------------+
                |    API Gateway    |
                +---------+---------+
                          |
        -----------------------------------------------
        |                    |                        |
        v                    v                        v
+---------------+    +---------------+       +---------------+
| Order Service |    | Payment Svc   |       | User Service  |
+-------+-------+    +-------+-------+       +-------+-------+
        \                  |                       /
         \                 |                      /
          \                |                     /
           --------------------------------------
                          |
                          v
                 +-------------------+
                 |   Shared Database |
                 +-------------------+
```

## Can it still be called microservices?

In practice, teams may still call it microservices, but architecturally it is usually a **compromised** or **transitional** design.

Why:
- Database ownership is not separated
- Coupling remains high
- Independent deployment becomes weaker
- Schema changes affect multiple services

## Why companies do this

- Easier migration from monolith
- Existing database is hard to split
- Short-term simplicity
- Fear of distributed transactions
- Limited time or platform maturity

## Shared DB advantages

| Advantage | Value |
|---|---|
| Easier migration | Split code before splitting data |
| Simpler short-term reporting | All data is in one place |
| Fewer distributed transaction issues at first | One DB may still handle ACID locally |
| Lower short-term complexity | Data separation can be delayed |

## Shared DB disadvantages

| Disadvantage | Why it matters |
|---|---|
| Tight coupling | Schema changes affect many services |
| Deployment coordination | Teams must release together more often |
| Ownership confusion | Unclear who owns tables and schema |
| Database bottleneck | One DB becomes common dependency |
| Poor fault isolation | DB outage affects many services |
| Hidden dependencies | Services start reading each other's tables |

## Why separate database per service is preferred

Because it gives:
- Clear ownership
- Better autonomy
- Safer schema evolution
- Better independent deployment
- Better accountability per team

The tradeoff is more complexity in:
- Data duplication
- Event propagation
- Reporting
- Eventual consistency

## Shared DB and distributed monoliths

Shared database is one of the biggest reasons teams accidentally create a **distributed monolith**.

Common signs:
- Many services read or write same tables
- DB changes require coordinated releases
- Cross-service reporting depends on direct joins in the shared schema
- One DB issue affects many services together

## Better direction

```text
                +-------------------+
                |    API Gateway    |
                +---------+---------+
                          |
      ---------------------------------------------------
      |                        |                        |
      v                        v                        v
+-------------+         +-------------+          +-------------+
| Order Svc   |         | Payment Svc |          | User Svc    |
+------+------+         +------+------+          +------+------+
       |                       |                        |
       v                       v                        v
  Order DB                Payment DB                 User DB
       |                       |
       +----------- Events / APIs -----------+
```

## Migration away from shared DB

1. Identify table ownership
2. Stop direct cross-service table access
3. Introduce APIs or events for data sharing
4. Extract one domain at a time
5. Move owned data into service-specific storage
6. Remove old shared-schema dependencies gradually

## Important interview points

- Shared DB can exist in real systems, especially during migration
- It is usually not the ideal end state
- Separate database per service is preferred mainly for autonomy and loose coupling

---

# 8. Important Interview Keywords

| Term | Meaning |
|---|---|
| Microservices | Small, focused, independently deployable services aligned to business capabilities |
| Monolith | Single deployable application |
| Loose Coupling | Minimal dependency on another service's internals |
| High Cohesion | Related functionality stays together in one service |
| Independent Deployment | One service can be released separately |
| Business Capability | Real business function like payments or orders |
| Bounded Context | Domain boundary with a clear model |
| API Gateway | Client entry point for routing and cross-cutting concerns |
| Service Discovery | Mechanism for finding services dynamically |
| Fault Isolation | One failure should not take down everything |
| Resilience | Ability to keep operating during faults |
| Eventual Consistency | State becomes consistent over time |
| Saga Pattern | Distributed workflow using local transactions and compensation |
| Circuit Breaker | Stops repeated calls to a failing dependency |
| Timeout | Maximum wait time for a dependency |
| Retry | Reattempting transient failures carefully |
| Bulkhead | Resource isolation to prevent widespread failure |
| CQRS | Separate write and read models when useful |
| Event-Driven Architecture | Services react to published events |
| Distributed Monolith | Separate services with tight coupling |
| Modular Monolith | Single deployable app with strong internal module boundaries |
| Observability | Understanding the system through logs, metrics, and traces |
| Backward Compatibility | New versions keep supporting existing consumers |
| CAP Theorem | Tradeoff around consistency, availability, and partition tolerance |

---

# 9. Most Asked Interview Questions

## 1. What are microservices?
Microservices are an architectural style where an application is built as a set of small, focused, loosely coupled services aligned to business capabilities. These services communicate over the network and can usually be developed, deployed, and scaled independently.

## 2. Why do companies use microservices?
Companies use microservices for independent deployments, independent scaling, team autonomy, better fault isolation, and better management of large and complex domains.

## 3. Are microservices always better than monoliths?
No. They are useful only when scale, team size, domain complexity, and operational maturity justify the added distributed systems complexity.

## 4. Why are microservices hard?
Because they are distributed systems. That introduces latency, partial failures, data consistency issues, observability challenges, versioning, deployment complexity, and debugging difficulty.

## 5. What is the difference between monolith and microservices?
A monolith is one deployable application. Microservices split the application into multiple independently deployable services. Monoliths are simpler initially; microservices are better for larger and more complex systems if designed and operated well.

## 6. What are the main characteristics of microservices?
Loose coupling, high cohesion, business capability ownership, independent deployment, decentralized data ownership, scalability, fault isolation, resilience, and automation.

## 7. Can microservices share a database?
They can in practice, especially during migration, but it is usually discouraged because it creates tight coupling and often leads to a distributed monolith.

## 8. What is eventual consistency?
It means the system may not show the same data everywhere immediately, but it becomes consistent over time.

## 9. What is Saga pattern?
Saga is a pattern for managing distributed business transactions using local transactions and compensating actions instead of one global transaction.

## 10. What is a distributed monolith?
A distributed monolith is a system split into services physically but still tightly coupled through shared databases, synchronized deployments, or strong runtime dependencies.

## 11. What is the role of API Gateway?
It acts as the client-facing entry point and handles routing, security, rate limiting, and sometimes response aggregation.

## 12. Why is observability important in microservices?
Because requests flow across many services, so logs, metrics, and traces are needed to understand performance and failures.

## 13. What is loose coupling and high cohesion?
Loose coupling means services should depend minimally on each other. High cohesion means one service should contain closely related responsibilities.

## 14. When would you choose microservices?
When the system has clear domain boundaries, many teams need autonomy, different parts scale differently, and the organization is mature enough to operate distributed systems.

## 15. What is Conway's Law?
It says system design tends to reflect the communication structure of the organization. This is one reason team boundaries often shape service boundaries.

---

# 10. Common Beginner Mistakes

1. Thinking microservices are always better than monoliths
2. Splitting too early without clear domain boundaries
3. Splitting by technical layers instead of business capabilities
4. Sharing one database across many services forever
5. Ignoring observability and tracing
6. Overusing synchronous communication
7. Ignoring backward compatibility
8. Treating “micro” as “tiny”
9. Believing every service should use a different technology
10. Copying Netflix, Amazon, or Uber architecture without similar scale or maturity

---

# 11. Key Takeaways

- Microservices are mainly about **autonomy, ownership, and scaling large systems**, not just smaller code files
- Their biggest strength is **independent deployment and independent scaling**
- Their biggest cost is **distributed systems complexity**
- Good service boundaries matter more than the number of services
- Database-per-service is preferred because it protects autonomy
- Observability, resilience, and automation are essential in real microservices systems
- A modular monolith is often the right starting point
- A distributed monolith is a major warning sign
- Shared database architecture is common during migration, but risky as a long-term design

---

# 12. Final Summary

Microservices architecture builds an application as a set of **small, focused, business-aligned services** that can evolve more independently than a monolith. Companies adopt microservices mainly to improve team autonomy, release speed, scaling flexibility, and fault isolation in large and complex systems.

But microservices are not automatically better. They are distributed systems, so they bring latency, partial failures, data consistency issues, debugging difficulty, deployment complexity, and major operational overhead. That is why strong microservices systems always depend on good boundaries, automation, observability, resilience patterns, and mature engineering practices.

## Final interview revision points

1. Microservices are independently deployable, business-focused services.
2. They work best when scale, team size, and domain complexity grow.
3. They improve autonomy, scaling flexibility, and fault isolation.
4. They increase distributed systems complexity.
5. Loose coupling and high cohesion are core design goals.
6. Database-per-service is preferred for autonomy.
7. Shared DB often leads to distributed monolith problems.
8. Monolith is still a valid and often correct choice.
9. Modular monolith is a strong starting architecture.
10. In interviews, always explain both benefits and tradeoffs.
