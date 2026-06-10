# Spring Cloud Load Balancing and Resilience Notes

> Notes on Spring Cloud LoadBalancer, timeouts, retries, circuit breakers, bulkheads, and practical failure handling.

---

## 1. Why Load Balancing is needed

In microservices, one service often runs as multiple instances.

Example:
- `PAYMENT-SERVICE` instance 1
- `PAYMENT-SERVICE` instance 2
- `PAYMENT-SERVICE` instance 3

If a caller wants to invoke payment service, which instance should it call?
That is where load balancing is needed.

---

## 2. What is Load Balancing?

Load balancing means distributing requests across multiple instances of the same service.

### Benefits
- better traffic distribution
- better availability
- supports scaling
- reduces overload on one instance

---

## 3. Client-side vs server-side load balancing

| Type | Meaning | Example |
|---|---|---|
| Client-side | Caller chooses service instance | Spring Cloud LoadBalancer |
| Server-side | A reverse proxy/load balancer chooses instance | Nginx, external LB |

### Interview point
In Spring Cloud microservices, **client-side load balancing** is a common answer.

---

## 4. Spring Cloud LoadBalancer

Spring Cloud LoadBalancer is the Spring solution that selects a target service instance on the client side.

It commonly works with:
- FeignClient
- WebClient
- RestTemplate
- Spring Cloud Gateway

---

## 5. How it works

```text
Order Service wants PAYMENT-SERVICE
   -> discovery finds available payment instances
   -> load balancer selects one instance
   -> request sent to selected instance
```

### Example
```text
PAYMENT-SERVICE instances:
- 10.0.0.2:8080
- 10.0.0.5:8080
- 10.0.0.8:8080

Load balancer chooses one for each request.
```

---

## 6. Common strategy

Most beginner-level discussions assume **round-robin** style selection.

That means requests are distributed one by one across instances.

### Important note
Actual behavior can vary based on configuration and environment, but round-robin is the common interview explanation.

---

## 7. Gateway and Load Balancing

When Gateway uses:
```text
lb://ORDER-SERVICE
```
that means:
- resolve `ORDER-SERVICE`
- get available instances
- choose one instance using load balancing
- forward request there

So load balancing is not only for Feign. It also appears in Gateway routing.

---

## 8. Why resilience is needed

In distributed systems:
- services can be slow
- service can be partially down
- network can fail
- one dependency can impact many callers

Without resilience:
- failures spread
- request threads get blocked
- system becomes unstable

---

## 9. Most important resilience patterns

## Timeout
Stop waiting after a fixed time.

### Why important
If one dependency is slow, caller should not wait forever.

---

## Retry
Retry a failed request when failure may be temporary.

### Good use
- transient network issue
- short service hiccup

### Risk
Too many retries can increase load and make outage worse.

---

## Circuit Breaker
Stops repeated calls to a failing dependency for some time.

### Why important
It prevents the caller from wasting resources on a service that is already failing.

### States to know
- Closed: normal traffic flows
- Open: calls are blocked temporarily
- Half-open: test if dependency recovered

---

## Bulkhead
Isolates resources so one failing dependency does not consume everything.

Example:
- separate thread pool / limits for payment calls
- payment issue should not block all order processing threads

---

## Fallback
Provide degraded behavior when dependency fails.

Example:
- recommendation service failure returns empty list
- payment failure should return proper error, not fake success

---

## 10. Resilience4j

Resilience4j is commonly used with Spring Boot/Spring Cloud for:
- circuit breaker
- retry
- rate limiter
- bulkhead
- timeout support patterns

### Interview point
If someone asks which library is commonly used for circuit breaker in Spring projects, a strong answer is **Resilience4j**.

---

## 11. Practical Failure Scenario

### Order -> Payment
```text
Client -> Gateway -> Order Service -> Payment Service
```

### Problem
Payment service becomes slow.

### Without resilience
- order requests keep waiting
- threads get blocked
- latency rises
- checkout starts failing badly

### With resilience
- timeout triggers quickly
- circuit breaker opens if failures continue
- fallback or controlled error response is returned
- system fails more gracefully

---

## 12. Retry: when to be careful

Do not blindly retry:
- payment charge calls
- non-idempotent operations
- already overloaded services

### Why?
Because retry may create:
- duplicate actions
- more traffic
- worse outages

### Interview line
Retry is useful only when operation is safe and failure is likely temporary.

---

## 13. Idempotency and Resilience

This is important in interviews.

If an operation may be retried, it should ideally be **idempotent** or protected with unique request handling.

Example:
- charging the same payment twice is dangerous
- creating same order twice is dangerous

So retry must be designed carefully.

---

## 14. Common Interview Questions

### What is client-side load balancing?
The caller itself selects one instance from available service instances before making the request.

### How does Feign use load balancing?
Feign works with discovery and Spring Cloud LoadBalancer to choose one instance of the target service.

### Why is timeout important?
Because a slow dependency can otherwise block resources and increase system-wide latency.

### What is circuit breaker?
A pattern that stops repeated calls to a failing dependency to prevent cascading failures.

### Difference between retry and circuit breaker?
Retry tries again for temporary failures. Circuit breaker stops calling when failures are persistent.

---

## 15. Common Mistakes

- not setting timeouts
- retrying every failure blindly
- using fallback for critical operations without clear business rules
- thinking more retries always improve reliability
- not understanding idempotency
- ignoring thread/resource isolation

---

## 16. Important Interview Points

- Load balancing helps distribute traffic across instances.
- Spring Cloud commonly uses client-side load balancing.
- `lb://SERVICE-NAME` is tied to discovery + load balancing.
- Resilience patterns are required because remote calls fail.
- Circuit breaker protects the system from cascading failures.
- Retry must be careful, limited, and usually combined with timeout.

---

## 17. Key Takeaways

- Scaling creates multiple service instances; load balancing makes them usable.
- Load balancing improves availability and traffic distribution.
- Resilience is mandatory in microservices, not optional.
- Timeout, retry, circuit breaker, bulkhead, and fallback are key patterns.
- In interviews, explain both the pattern and when not to misuse it.

