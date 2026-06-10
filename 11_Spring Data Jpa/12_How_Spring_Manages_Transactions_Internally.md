# Spring Transactions – Part 2: How Spring Manages Transactions Internally

---

# 1️⃣ From Database Transactions to Spring Transactions

In databases, transactions are controlled manually.

Example:

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

But in enterprise applications, we do not manage transactions manually.

Frameworks like **Spring** handle this automatically.

Example:

```java
@Transactional
public void transferMoney() {
    withdraw();
    deposit();
}
```

Spring automatically:

- Starts transaction
- Executes method
- Commits if successful
- Rolls back if exception occurs

---

# 2️⃣ What is `@Transactional`?

`@Transactional` is a Spring annotation that tells Spring:

> Execute this method inside a database transaction.

Example:

```java
@Service
public class BankService {

    @Transactional
    public void transferMoney() {
        withdraw();
        deposit();
    }
}
```

Spring automatically:

```
Start Transaction
      ↓
Execute Method
      ↓
Commit OR Rollback
```

---

# 3️⃣ Where Should `@Transactional` Be Used?

Best practice:

Use `@Transactional` at the **service layer**.

Example architecture:

```
Controller
   ↓
Service (@Transactional)
   ↓
Repository
   ↓
Database
```

Why?

Service layer represents **business transaction boundary**.

Example:

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder() {
        saveOrder();
        reduceInventory();
        processPayment();
    }
}
```

All operations must succeed together.

---

# 4️⃣ How Spring Implements Transactions

Spring uses **AOP (Aspect-Oriented Programming)**.

It creates a **proxy object** around the service class.

Flow:

```
Client
   ↓
Spring Proxy
   ↓
Start Transaction
   ↓
Actual Method Execution
   ↓
Commit or Rollback
```

Important:

The transaction is managed by the **proxy**, not the method itself.

---

# 5️⃣ PlatformTransactionManager

Spring uses an abstraction called:

```
PlatformTransactionManager
```

This interface handles transaction management.

Implementations:

| Database Type | Transaction Manager |
|---------------|---------------------|
| JDBC | DataSourceTransactionManager |
| JPA | JpaTransactionManager |
| Hibernate | HibernateTransactionManager |

Example configuration happens automatically in Spring Boot.

---

# 6️⃣ Transaction Lifecycle in Spring

Typical transaction flow:

```
Client calls service method
        ↓
Spring Proxy intercepts method
        ↓
Transaction starts
        ↓
Business logic executes
        ↓
If success → COMMIT
If exception → ROLLBACK
```

Example:

```java
@Transactional
public void createOrder() {
    orderRepository.save(order);
    inventoryService.updateStock();
}
```

---

# 7️⃣ Rollback Rules (Important)

By default, Spring rolls back transaction only for:

```
RuntimeException
Error
```

Example:

```java
throw new RuntimeException();
```

Triggers rollback.

But checked exceptions do NOT trigger rollback.

Example:

```java
throw new Exception();
```

No rollback unless configured.

---

# 8️⃣ Forcing Rollback on Checked Exception

Example:

```java
@Transactional(rollbackFor = Exception.class)
public void processPayment() throws Exception {
    // logic
}
```

Now checked exceptions will also rollback.

---

# 9️⃣ Declarative vs Programmatic Transactions

---

## Declarative Transactions (Most Common)

Using annotations.

Example:

```java
@Transactional
public void transferMoney() {
}
```

Advantages:

- Clean code
- Easy configuration
- Recommended approach

---

## Programmatic Transactions

Manual control using `TransactionTemplate`.

Example:

```java
transactionTemplate.execute(status -> {
    // transaction logic
    return null;
});
```

Used rarely when fine-grained control is needed.

---

# 🔟 Transaction Propagation Preview

Sometimes a transactional method calls another transactional method.

Example:

```
Method A → Method B
```

Should B:

- join existing transaction?
- create new transaction?

This behavior is controlled by **Propagation**.

Example:

```java
@Transactional(propagation = Propagation.REQUIRED)
```

We will cover propagation deeply in Part 3.

---

# 1️⃣1️⃣ Why Understanding Internals Matters

Many bugs occur because developers don't understand:

- proxy behavior
- transaction boundaries
- rollback rules

Example bug:

```
@Transactional not working
```

Often caused by **calling method inside same class**.

This will be explained in later parts.

---

# 1️⃣2️⃣ Most Asked Interview Questions

- How does `@Transactional` work internally?
- What is PlatformTransactionManager?
- Why use `@Transactional` at service layer?
- Why does Spring use proxy?
- When does Spring rollback transaction?
- What is declarative transaction management?

---

# 1️⃣3️⃣ Key Concepts to Remember

Spring transaction management relies on:

```
AOP
Proxy
PlatformTransactionManager
@Transactional annotation
```

Understanding these makes debugging much easier.

---

# 1️⃣4️⃣ Interview Summary (≈30 words)

Spring manages transactions declaratively using `@Transactional`, implemented through AOP proxies and PlatformTransactionManager, automatically starting, committing, or rolling back transactions around service-layer business logic.

---