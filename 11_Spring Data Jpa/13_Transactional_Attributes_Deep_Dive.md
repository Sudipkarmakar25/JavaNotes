# Spring Transactions – Part 3: `@Transactional` Attributes Deep Dive

---

# 1️⃣ What Are Transaction Attributes?

`@Transactional` has several attributes that control **how the transaction behaves**.

Example:

```java
@Transactional(
    propagation = Propagation.REQUIRED,
    isolation = Isolation.READ_COMMITTED,
    readOnly = false,
    timeout = 30,
    rollbackFor = Exception.class
)
```

These attributes define:

- How transactions interact
- How data is isolated
- How rollback occurs
- Performance optimizations

---

# 2️⃣ Propagation (Very Important)

Propagation defines:

> What should happen if a transactional method is called inside another transaction.

Example:

```
Method A (transaction)
   ↓
Method B (@Transactional)
```

Should B join A or create a new transaction?

Propagation controls this behavior.

---

## Propagation Types

Spring provides **7 propagation types**.

---

## REQUIRED (Default)

```
Join existing transaction
OR
Create new transaction
```

Example:

```java
@Transactional(propagation = Propagation.REQUIRED)
```

Flow:

```
A transaction exists → join it
No transaction → create new
```

Most commonly used.

---

## REQUIRES_NEW

Always creates **new transaction**.

Suspends existing transaction.

Example:

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

Flow:

```
Existing Transaction
        ↓
Suspend Transaction
        ↓
Start New Transaction
        ↓
Execute Method
        ↓
Commit New Transaction
        ↓
Resume Old Transaction
```

Used for:

- Logging
- Auditing
- Independent operations

---

## SUPPORTS

If transaction exists → join it  
If not → execute without transaction

Example:

```java
@Transactional(propagation = Propagation.SUPPORTS)
```

Useful for **read-only operations**.

---

## NOT_SUPPORTED

Execute method **without transaction**.

If transaction exists → suspend it.

Example:

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
```

Used when transactions are unnecessary.

---

## MANDATORY

Requires existing transaction.

If none exists → exception thrown.

Example:

```java
@Transactional(propagation = Propagation.MANDATORY)
```

Used when method must always run inside a transaction.

---

## NEVER

Method must NOT run inside a transaction.

If transaction exists → exception thrown.

Example:

```java
@Transactional(propagation = Propagation.NEVER)
```

Rarely used.

---

## NESTED

Creates **nested transaction inside existing transaction**.

Uses database **savepoints**.

Example:

```java
@Transactional(propagation = Propagation.NESTED)
```

Rollback affects only nested block.

---

# 3️⃣ Isolation Levels (Very Important)

Isolation controls how transactions interact with each other.

Problem:

Multiple transactions may read/write the same data simultaneously.

Isolation levels prevent data inconsistencies.

---

## Isolation Problems

---

### Dirty Read

Reading uncommitted data.

Example:

```
Transaction A updates balance = 500
Transaction B reads balance = 500
Transaction A rolls back
```

B read invalid data.

---

### Non-Repeatable Read

Reading same row twice returns different values.

Example:

```
Transaction A reads balance = 1000
Transaction B updates balance = 500
Transaction A reads again → 500
```

Data changed mid-transaction.

---

### Phantom Read

New rows appear during transaction.

Example:

```
Transaction A reads 10 orders
Transaction B inserts new order
Transaction A reads again → 11 orders
```

---

# 4️⃣ Isolation Levels in Spring

---

## READ_UNCOMMITTED

Allows:

- Dirty reads
- Non-repeatable reads
- Phantom reads

Fastest but unsafe.

---

## READ_COMMITTED

Prevents dirty reads.

Allows:

- Non-repeatable reads
- Phantom reads

Most common default in databases.

---

## REPEATABLE_READ

Prevents:

- Dirty reads
- Non-repeatable reads

Still allows phantom reads.

Default in MySQL.

---

## SERIALIZABLE

Highest isolation.

Prevents:

- Dirty reads
- Non-repeatable reads
- Phantom reads

But slowest because it locks data heavily.

---

# 5️⃣ readOnly Attribute

Example:

```java
@Transactional(readOnly = true)
```

Used for:

- Read-only operations
- Performance optimization

Benefits:

- Prevents accidental updates
- Improves database optimization

Example:

```java
@Transactional(readOnly = true)
public List<User> getUsers() {
    return repository.findAll();
}
```

---

# 6️⃣ timeout Attribute

Defines maximum execution time.

Example:

```java
@Transactional(timeout = 10)
```

If transaction takes longer than 10 seconds:

Transaction is rolled back.

Useful to prevent **long-running transactions**.

---

# 7️⃣ rollbackFor Attribute

By default:

Spring rolls back only on **RuntimeException**.

Example:

```java
throw new RuntimeException();
```

Triggers rollback.

To rollback for checked exceptions:

```java
@Transactional(rollbackFor = Exception.class)
```

---

# 8️⃣ noRollbackFor Attribute

Sometimes we want transaction to **commit even if exception occurs**.

Example:

```java
@Transactional(noRollbackFor = CustomException.class)
```

Transaction will commit even when exception is thrown.

Rarely used but important.

---

# 9️⃣ Example Using Multiple Attributes

```java
@Transactional(
    propagation = Propagation.REQUIRED,
    isolation = Isolation.READ_COMMITTED,
    readOnly = false,
    timeout = 30,
    rollbackFor = Exception.class
)
public void processPayment() {
}
```

This configuration defines:

- transaction behavior
- concurrency rules
- rollback behavior

---

# 🔟 Real-World Example

Example: Order processing.

```
Place Order
   ↓
Save Order
   ↓
Update Inventory
   ↓
Process Payment
```

All operations must run inside **one transaction**.

If payment fails:

Entire transaction rolls back.

---

# 1️⃣1️⃣ Most Asked Interview Questions

- What are propagation types?
- Difference between REQUIRED and REQUIRES_NEW?
- What is isolation level?
- What is dirty read?
- What is phantom read?
- Why use readOnly transactions?
- What is rollbackFor?

---

# 1️⃣2️⃣ Key Concept to Remember

Transactions are controlled by:

```
Propagation
Isolation
Rollback rules
Timeout
Read-only optimization
```

These attributes allow fine-grained control over transactional behavior.

---

# 1️⃣3️⃣ Interview Summary (≈30 words)

`@Transactional` attributes define transaction behavior including propagation, isolation levels, rollback rules, read-only optimization, and timeout control, allowing developers to manage concurrency, consistency, and reliability of database operations.

---