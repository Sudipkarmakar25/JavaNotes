# Spring Cloud API Gateway Notes

> Practical notes on API Gateway in Spring Cloud for interviews and real project understanding.

---

## 1. What is API Gateway?

API Gateway is the **single entry point** for client requests in a microservices system.

Instead of the client calling many services directly, the client calls the gateway, and the gateway routes the request to the correct service.

### Simple answer
API Gateway stands in front of microservices and manages incoming traffic.

---

## 2. Why API Gateway is used

Without API Gateway:
- client must know each service URL
- frontend handles too much routing knowledge
- auth logic gets repeated in multiple services
- CORS, rate limiting, and filters become harder to manage

With API Gateway:
- one public entry point
- better control over incoming traffic
- easier routing and cross-cutting concerns
- internal services stay hidden from clients

---

## 3. Request Flow

```text
Client
  |
  v
API Gateway
  |
  +--> Auth check / token validation
  |
  +--> Route matching
  |
  +--> Optional filters
  |
  v
Target Microservice
```

### Example
```text
GET /orders/101
   -> API Gateway
   -> route matched to ORDER-SERVICE
   -> request forwarded to one instance of order service
```

---

## 4. Main responsibilities of API Gateway

- route request to correct service
- hide internal service URLs
- validate tokens or forward auth information
- apply filters
- rate limit requests
- handle CORS
- add request headers
- log or trace incoming requests
- sometimes aggregate responses

### Important interview point
API Gateway should handle **gateway concerns**, not core business logic.

Bad practice:
- putting full order creation logic inside gateway

Good practice:
- gateway only routes to `Order Service`

---

## 5. Spring Cloud Gateway

Spring Cloud Gateway is the gateway solution commonly used in Spring Cloud.

### Why it is preferred
- built for reactive routing
- supports predicates and filters
- integrates with discovery and load balancing
- supports route definitions in config

---

## 6. Route, Predicate, Filter

## Route
A route tells gateway where to forward a request.

## Predicate
A predicate is the condition for matching a request.

Example:
- path is `/orders/**`
- method is `GET`
- header contains a value

## Filter
A filter runs custom logic before or after forwarding.

Example:
- add request header
- check auth token
- log request time
- rewrite path

---

## 7. Example Gateway Configuration

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service-route
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/orders/**
          filters:
            - AddRequestHeader=X-Request-Source, gateway
```

### What this means
- requests coming to `/orders/**` match this route
- `lb://ORDER-SERVICE` means use load balancing with service discovery
- request goes to one instance of `ORDER-SERVICE`

---

## 8. Static routing vs discovery-based routing

| Type | Meaning | Example |
|---|---|---|
| Static route | Hardcoded target URL | `http://localhost:8081` |
| Discovery-based route | Use service name and registry | `lb://ORDER-SERVICE` |

### Interview Note
In real microservices, discovery-based routing is preferred because instances may change dynamically.

---

## 9. API Gateway with Eureka

When Gateway is connected to Eureka:
- services register themselves with Eureka
- Gateway can route using service names
- no need to hardcode every service instance address

```text
Client -> Gateway -> Eureka lookup -> ORDER-SERVICE instance -> response
```

---

## 10. API Gateway and Security

Gateway is commonly used as an authentication entry layer.

It may:
- validate JWT token
- reject unauthorized requests
- forward user info in headers
- apply public/private route rules

### Important point
Gateway can help with security, but internal services may still need authorization checks.

---

## 11. Common Gateway Features in Real Projects

- path-based routing
- header-based routing
- request/response logging
- token validation
- rate limiting
- circuit breaker at gateway route level
- fallback route
- path rewriting
- correlation ID propagation

---

## 12. Real-World Example

### E-commerce system
Routes may look like:
- `/api/users/**` -> `USER-SERVICE`
- `/api/orders/**` -> `ORDER-SERVICE`
- `/api/payments/**` -> `PAYMENT-SERVICE`

Client only knows one domain:
```text
https://shop.example.com/api/orders/123
```

Client does not need to know:
- order service port
- internal network URL
- how many instances are running

---

## 13. Benefits of API Gateway

- simpler frontend integration
- centralized routing
- reduced client complexity
- easier monitoring of incoming traffic
- common filter logic in one place
- internal topology hidden from clients

## Tradeoffs
- gateway becomes critical infrastructure
- if badly designed, it becomes a bottleneck
- too much logic in gateway creates coupling
- extra hop adds slight latency

---

## 14. Common Interview Questions

### What is API Gateway?
A single entry point that routes client requests to the correct microservice and handles cross-cutting concerns.

### Why not let frontend call services directly?
Because that increases client complexity, exposes internal service structure, and duplicates concerns like auth, routing, and rate limiting.

### What is `lb://` in Spring Cloud Gateway?
It means route using service name with load-balanced discovery instead of a fixed URL.

### Can API Gateway replace service-to-service communication?
No. Gateway is mainly for client-to-service traffic, not a replacement for internal service communication.

### Should business logic be inside API Gateway?
No. Only gateway concerns should stay there.

---

## 15. Common Mistakes

- using gateway for heavy business logic
- hardcoding service URLs instead of using discovery
- not adding proper timeouts
- not propagating correlation/request IDs
- putting all security trust only at gateway and none in services
- creating too many complex route rules without documentation

---

## 16. Important Interview Points

- API Gateway reduces client complexity.
- Spring Cloud Gateway commonly works with Eureka and LoadBalancer.
- `lb://SERVICE-NAME` is a very common interview point.
- Gateway is useful for routing, auth filters, logging, and rate limiting.
- Gateway should not become a mini-monolith.

---

## 17. Key Takeaways

- API Gateway is the front door of a microservices system.
- It routes, filters, and centralizes common request concerns.
- In Spring Cloud, Gateway integrates naturally with service discovery.
- Use gateway for infrastructure concerns, not domain logic.

