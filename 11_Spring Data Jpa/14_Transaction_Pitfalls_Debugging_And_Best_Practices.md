# Spring Transactions – Part 4: Real-World Pitfalls, Debugging & Best Practices

---

# 1️⃣ Why Transaction Bugs Happen in Real Projects

Most transaction issues occur because developers misunderstand:

- How Spring proxies work
- Transaction boundaries
- Lazy loading behavior
- Thread boundaries
- Method visibility rules

Many bugs look like:

```
@Transactional is not working
```

But the real issue is usually **proxy limitations or incorrect architecture**.

---

# 2️⃣ The Self-Invocation Problem (Very Important)

Spring transactions work through **proxies**.

Example:

```
Client → Spring Proxy → Service Method
```

The proxy starts and commits the transaction.

Problem occurs when a method calls another transactional method **inside the same class**.

Example:

```java
@Service
public class OrderService {

    public void placeOrder() {
        saveOrder();   // internal call
    }

    @Transactional
    public void saveOrder() {
        // database operation
    }
}
```

Here:

```
placeOrder() → saveOrder()
```

This call **does not go through the proxy**.

Result:

```
@Transactional is ignored
```

---

## Solution

Call transactional method from **another Spring bean**.

Example:

```
Controller → ServiceA → ServiceB (@Transactional)
```

Now proxy works correctly.

---

# 3️⃣ Private Methods Cannot Be Transactional

Spring transactions only work on:

```
public methods
```

Example:

```java
@Transactional
private void saveData() {
}
```

This will NOT work.

Why?

Spring AOP proxies intercept **public methods only**.

---

# 4️⃣ LazyInitializationException (Common Production Issue)

Occurs when:

- Entity has LAZY relationship
- Session/transaction is closed
- Lazy field is accessed later

Example:

```java
@Transactional
public User getUser() {
    return userRepository.findById(1L).get();
}
```

Later:

```java
user.getOrders().size();
```

If session is closed:

```
LazyInitializationException
```

---

## Solutions

- Use `JOIN FETCH`
- Convert to DTO inside transaction
- Fetch required data before returning

Example:

```java
SELECT u FROM User u JOIN FETCH u.orders
```

---

# 5️⃣ Transactions Do Not Work with Async

Example:

```java
@Async
@Transactional
public void processOrder() {
}
```

Problem:

`@Async` runs in **different thread**.

Transactions are **thread-bound**.

Therefore transaction context is not shared.

---

# 6️⃣ Long-Running Transactions (Dangerous)

Bad example:

```java
@Transactional
public void processOrder() {

    saveOrder();

    callExternalPaymentAPI(); // slow network call

    updateInventory();
}
```

Problem:

- DB connection locked
- Other transactions blocked
- Performance degradation

Best practice:

Keep transactions **short and focused**.

---

# 7️⃣ Transaction Boundaries (Important Design Principle)

Transaction should wrap **business logic**, not technical layers.

Correct structure:

```
Controller
   ↓
Service (@Transactional)
   ↓
Repository
```

Incorrect structure:

```
@Transactional in controller
```

Controllers should not manage transactions.

---

# 8️⃣ Nested Transactions in Real Systems

Example:

```
Place Order
    ↓
Save Order
    ↓
Audit Log
```

Audit logging may use:

```
REQUIRES_NEW
```

Example:

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void logAudit() {
}
```

Even if outer transaction fails:

Audit log is saved.

---

# 9️⃣ Debugging Transaction Issues

Useful steps:

### Enable SQL logging

```
spring.jpa.show-sql=true
```

### Enable transaction logs

```
logging.level.org.springframework.transaction=TRACE
```

This helps track:

```
Transaction start
Transaction commit
Transaction rollback
```

---

# 🔟 Best Practices for Transactions

---

## Use Service Layer

```
@Transactional at service layer
```

---

## Keep Transactions Short

Avoid:

- network calls
- heavy loops
- slow operations

---

## Prefer Read-Only Transactions for Queries

```java
@Transactional(readOnly = true)
```

Benefits:

- better DB optimization
- prevents accidental updates

---

## Handle Exceptions Carefully

Remember:

```
RuntimeException → rollback
Checked Exception → no rollback (unless configured)
```

---

## Avoid Large Persistence Context

Loading thousands of entities in one transaction can cause:

- memory issues
- slow performance

---

# 1️⃣1️⃣ Transaction Design in Microservices

Transactions cannot span multiple microservices easily.

Instead systems use:

```
Event-driven architecture
Saga pattern
Compensating transactions
```

Example:

```
Order Service → Payment Service → Inventory Service
```

Distributed transactions are complex.

---

# 1️⃣2️⃣ Most Asked Interview Questions

- Why does `@Transactional` fail in self-invocation?
- Why doesn't `@Transactional` work on private methods?
- Why do lazy loading exceptions occur?
- Why transactions should be short?
- What happens when using `@Async` with transactions?
- Why service layer is preferred for transactions?

---

# 1️⃣3️⃣ Key Lessons to Remember

```
Transactions depend on proxies
Transactions are thread-bound
Transactions should be short
Transactions belong in service layer
```

Understanding these prevents most production issues.

---

# 1️⃣4️⃣ Interview Summary (≈30 words)

Mastering Spring transactions requires understanding proxy-based execution, transaction boundaries, propagation behavior, lazy loading interactions, and real-world pitfalls such as self-invocation, async limitations, and long-running transactions.

---