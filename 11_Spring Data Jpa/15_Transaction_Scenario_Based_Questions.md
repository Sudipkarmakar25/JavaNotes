# Spring Transactions – Scenario-Based Questions (Part 1)

This set focuses on **real-world pitfalls and deeper transactional behavior** in Spring. Each scenario includes:

* Code
* What will happen?
* Detailed reasoning
* Key insight (interview-ready takeaway)

---

## 🔥 Scenario 1: Self Invocation (Proxy Limitation)

### Code

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder() {
        saveOrder();
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void saveOrder() {
        throw new RuntimeException("Failure");
    }
}
```

### What will happen?

* Will a new transaction be created?
* Will outer transaction commit or rollback?

### Answer

* `REQUIRES_NEW` will **NOT** be applied
* Entire transaction will **ROLLBACK**

### Detailed Reasoning

Spring uses **proxy-based AOP** to apply transactional behavior. When `placeOrder()` is called from outside (e.g., controller), it goes through the proxy, so transaction starts.

However, `saveOrder()` is called **internally (this.saveOrder())**:

* This call does NOT go through the proxy
* So `@Transactional(REQUIRES_NEW)` is completely ignored

Effectively, both methods run in the **same transaction**.

When exception occurs → transaction marked rollback → everything rolls back.

### Key Insight

> Transactional annotations only work when method is invoked through Spring proxy, not via internal calls.

---

## 🔥 Scenario 2: Checked Exception vs Rollback

### Code

```java
@Service
public class PaymentService {

    @Transactional
    public void processPayment() throws Exception {
        saveToDB();
        throw new Exception("Checked Exception");
    }

    private void saveToDB() {
        // DB insert
    }
}
```

### What will happen?

### Answer

* Transaction will **COMMIT** (no rollback)

### Detailed Reasoning

Spring's default rollback rules:

* Rollback on: `RuntimeException`, `Error`
* NO rollback on: Checked exceptions

Here:

* Exception is checked (`Exception`)
* Spring does NOT mark transaction for rollback
* DB insert remains committed

### Fix

```java
@Transactional(rollbackFor = Exception.class)
```

### Key Insight

> Not all exceptions trigger rollback — only unchecked ones by default.

---

## 🔥 Scenario 3: Exception Swallowing (Silent Commit Bug)

### Code

```java
@Transactional
public void createUser() {
    saveUser();
    try {
        saveAddress();
    } catch (Exception e) {
        // ignored
    }
}
```

### What will happen?

### Answer

* Transaction will **COMMIT**

### Detailed Reasoning

* `saveAddress()` throws exception
* Exception is caught and NOT rethrown
* Spring never sees failure

Transaction lifecycle:

1. Begin transaction
2. Exception occurs
3. Exception swallowed
4. Method completes normally
5. Spring commits transaction

This leads to **partial/inconsistent data**.

### Key Insight

> Catching exceptions without rethrowing breaks transactional guarantees.

---

## 🔥 Scenario 4: Flush vs Commit

### Code

```java
@Transactional
public void testFlush(EntityManager em) {
    User user = new User("A");
    em.persist(user);

    em.flush();

    throw new RuntimeException();
}
```

### What will happen?

### Answer

* Data will **NOT persist**

### Detailed Reasoning

* `persist()` → schedules insert
* `flush()` → executes SQL immediately

BUT:

* Still inside transaction
* No commit yet

When exception occurs:

* Transaction rolls back
* DB reverses changes

Even though SQL was executed earlier, it was **not finalized**.

### Key Insight

> Flush pushes SQL to DB, but only commit makes it permanent.

---

## 🔥 Scenario 5:

### Code

```java
@Transactional
public User getUser(Long id) {
    return userRepo.findById(id).get();
}
```

Controller:

```java
User user = userService.getUser(1L);
user.getOrders().size();
```

### What will happen?

### Answer

* `LazyInitializationException`

### Detailed Reasoning

* Transaction active inside service method
* Hibernate session open

After method returns:

* Transaction ends
* Session closes

Later:

* Lazy collection accessed (`orders`)
* No session available → exception thrown

### Fix Options

* Use `JOIN FETCH`
* Convert to DTO inside transaction
* Use OpenSessionInView (not ideal)

### Key Insight

> Lazy loading requires an active persistence context.

---

## 🔥 Scenario 6: Rollback-Only Marker

### Code

```java
@Transactional
public void outer() {
    try {
        inner();
    } catch (Exception e) {
        System.out.println("Handled");
    }
}

@Transactional
public void inner() {
    throw new RuntimeException();
}
```

### What will happen?

### Answer

* Transaction will **ROLLBACK**
* You may see: "Transaction marked as rollback-only"

### Detailed Reasoning

* `inner()` throws runtime exception
* Spring marks transaction as **rollback-only**

Even though exception is caught:

* Transaction state is already poisoned
* Commit is NOT allowed anymore

At commit time:

* Spring detects rollback-only flag
* Forces rollback

### Key Insight

> Once a transaction is marked rollback-only, it cannot be committed.

---

## 🔥 Scenario 7: readOnly Transaction Behavior

### Code

```java
@Transactional(readOnly = true)
public void updateUser() {
    user.setName("New");
    userRepo.save(user);
}
```

### What will happen?

### Answer

* Update may still **execute successfully**

### Detailed Reasoning

* `readOnly=true` is only a **hint**
* Hibernate may:

    * Skip dirty checking optimizations
    * But NOT strictly prevent writes

So depending on provider:

* Update might still go through

### Key Insight

> readOnly is an optimization hint, not a strict restriction.

---

## 🧠 Summary Takeaways

* Internal calls bypass transactional proxy
* Checked exceptions do not trigger rollback by default
* Swallowed exceptions cause silent commits
* Flush is not commit
* Lazy loading needs active session
* Rollback-only cannot be reversed
* readOnly is not enforced strictly

---
