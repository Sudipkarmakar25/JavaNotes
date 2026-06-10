# Spring Transactions – Part 1: Transaction Fundamentals

---

# 1️⃣ What is a Transaction?

A **transaction** is a group of database operations that are executed as **a single unit of work**.

The rule is simple:

- Either **all operations succeed**
- Or **none of them take effect**

This ensures **data consistency**.

Example:

Transferring money between bank accounts.

```
Withdraw money from Account A
Deposit money into Account B
```

If deposit fails after withdrawal, the system must **rollback** the withdrawal.

Without transactions, money would disappear.

---

# 2️⃣ Why Transactions Are Necessary

Imagine this code without transactions:

```java
withdraw(accountA, 100);
deposit(accountB, 100);
```

Scenario:

1. Money withdrawn from Account A
2. Server crashes before deposit

Result:

Account A → ₹100 less  
Account B → no change

Money is lost.

Transactions prevent this by guaranteeing **atomic operations**.

---

# 3️⃣ ACID Properties (Very Important)

Transactions follow **ACID properties**.

These are fundamental guarantees of reliable database systems.

---

## Atomicity

Atomicity means:

**All operations succeed or all fail.**

Example:

```
Debit A
Credit B
```

If credit fails → debit must rollback.

Atomicity ensures **no partial updates**.

---

## Consistency

Consistency means:

The database moves from **one valid state to another valid state**.

Example:

Total balance before transfer = total balance after transfer.

If any constraint is violated, the transaction fails.

---

## Isolation

Isolation ensures that **multiple transactions do not interfere with each other**.

Example:

Two users withdrawing money at the same time.

Without isolation:

```
User A reads balance = 1000
User B reads balance = 1000
User A withdraws 500
User B withdraws 500
```

Final balance becomes incorrect.

Isolation prevents this problem.

---

## Durability

Durability means:

Once a transaction is **committed**, its changes are permanent.

Even if:

- Server crashes
- Power failure occurs

The database guarantees committed data is not lost.

---

# 4️⃣ Transaction Lifecycle

Every transaction follows a lifecycle.

```
BEGIN TRANSACTION
       ↓
Execute SQL Operations
       ↓
COMMIT or ROLLBACK
```

Example:

```
START
 ↓
Update account A
 ↓
Update account B
 ↓
COMMIT
```

If something fails:

```
ROLLBACK
```

---

# 5️⃣ Commit vs Rollback

---

## Commit

Commit means:

All changes made during the transaction are **permanently saved**.

Example:

```sql
COMMIT;
```

After commit, changes cannot be undone.

---

## Rollback

Rollback cancels all changes made during the transaction.

Example:

```sql
ROLLBACK;
```

Database returns to the previous consistent state.

---

# 6️⃣ Example Transaction (SQL)

Example:

```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;

UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
```

If second update fails:

```
ROLLBACK;
```

This ensures money is not lost.

---

# 7️⃣ Transaction Boundaries

A **transaction boundary** defines where a transaction:

- starts
- ends

Example:

```
BEGIN TRANSACTION
    Business Logic
END TRANSACTION
```

Everything inside this boundary must succeed.

---

# 8️⃣ Database Transactions vs Application Transactions

---

## Database Transaction

Handled directly by database.

Example:

```
BEGIN
UPDATE
UPDATE
COMMIT
```

---

## Application Transaction

Handled by application frameworks like **Spring**.

Example:

```java
@Transactional
public void transferMoney() {
    withdraw();
    deposit();
}
```

Spring manages the transaction automatically.

---

# 9️⃣ Real-World Example (Bank Transfer)

Operation:

```
Transfer ₹100 from A → B
```

Steps:

1. Begin transaction
2. Deduct ₹100 from A
3. Add ₹100 to B
4. Commit transaction

If step 3 fails:

```
Rollback transaction
```

Final state remains consistent.

---

# 🔟 Problems Without Transactions

Without transactions:

- Partial updates occur
- Data becomes inconsistent
- Race conditions occur
- System becomes unreliable

Example issues:

- Double booking
- Lost money
- Incorrect inventory

---

# 1️⃣1️⃣ Where Transactions Are Used

Transactions are critical in:

- Banking systems
- E-commerce orders
- Payment processing
- Inventory management
- Booking systems

Anywhere **data integrity is critical**.

---

# 1️⃣2️⃣ Important Interview Questions

- What is a transaction?
- Explain ACID properties.
- Difference between commit and rollback?
- What is atomicity?
- Why isolation is important?
- What happens if transaction fails?
- Why transactions are critical in banking systems?

---

# 1️⃣3️⃣ Key Concept to Remember

Transactions ensure:

```
Correctness
Consistency
Reliability
```

They are the foundation of **all database operations**.

---

# 1️⃣4️⃣ Interview Summary (≈30 words)

A transaction is a unit of work that ensures multiple database operations either complete successfully together or fail together, maintaining data consistency through ACID properties: atomicity, consistency, isolation, and durability.

---