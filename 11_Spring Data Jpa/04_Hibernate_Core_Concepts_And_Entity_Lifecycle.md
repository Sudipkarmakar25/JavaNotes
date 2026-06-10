# Era 3 – Part 2: Hibernate Core Concepts (Most Important for Interviews)

---

# 1️⃣ Entity Lifecycle States (Very Important)

Every Hibernate entity moves through lifecycle states.

There are 4 states:

1. Transient
2. Persistent
3. Detached
4. Removed

---

## 1. Transient State

- Object created using `new`
- Not associated with Session
- Not stored in database
- No ID generated yet (usually)

Example:

```java
User user = new User();
user.setName("Rahul");
```

At this point:
- Hibernate does NOT know about this object
- No SQL executed

---

## 2. Persistent State

- Object is associated with an active Session
- Hibernate tracks changes
- Stored in first-level cache

Example:

```java
Session session = sessionFactory.openSession();
session.beginTransaction();

User user = new User();
user.setName("Rahul");

session.save(user); // now persistent
```

Now:
- Insert SQL will be generated
- Hibernate tracks changes automatically

---

## 3. Detached State

- Object was persistent
- Session is closed
- No longer tracked

Example:

```java
session.close();
```

Now:
- Changes to object will NOT be saved
- Must reattach using `merge()` or `update()`

---

## 4. Removed State

- Entity marked for deletion

Example:

```java
session.delete(user);
```

On commit:
```sql
DELETE FROM users WHERE id = ?
```

---

# 2️⃣ Dirty Checking (Very Important Concept)

Hibernate automatically detects changes in persistent objects.

Example:

```java
User user = session.get(User.class, 1L);
user.setName("Amit");
```

No explicit update call required.

On commit:

```sql
UPDATE users SET name = 'Amit' WHERE id = 1;
```

How it works:
- Hibernate keeps snapshot of object
- Compares changes before commit
- Automatically generates update SQL

This is called **Dirty Checking**.

---

# 3️⃣ First-Level Cache (Session Cache)

Every Session has its own cache.

Example:

```java
User user1 = session.get(User.class, 1L);
User user2 = session.get(User.class, 1L);
```

Second call:
- No SQL executed
- Returned from cache

Properties:
- Enabled by default
- Cannot be disabled
- Scoped to Session
- Improves performance

---

# 4️⃣ Flush vs Commit (Common Interview Trap)

## Flush

- Synchronizes Session state with DB
- Executes SQL
- Does NOT end transaction

```java
session.flush();
```

## Commit

- Flushes automatically
- Ends transaction
- Makes changes permanent

```java
transaction.commit();
```

Difference:

| Flush | Commit |
|-------|--------|
| Executes SQL | Executes SQL + ends transaction |
| Keeps transaction active | Ends transaction |

---

# 5️⃣ Session Lifecycle

Typical flow:

```
Open Session
     ↓
Begin Transaction
     ↓
Perform Operations
     ↓
Commit
     ↓
Close Session
```

Important:

- Session is NOT thread-safe
- Should be short-lived

---

# 6️⃣ Why Entity States Matter (Interview Angle)

Understanding entity states helps explain:

- Why updates sometimes don’t persist
- Why LazyInitializationException occurs
- Why merge() behaves differently
- Why detached entities cause confusion

This is a core Hibernate topic.

---

# 7️⃣ Most Common Interview Questions from This Section

- Explain entity lifecycle states.
- What is dirty checking?
- What is first-level cache?
- Difference between flush and commit?
- What happens when session is closed?
- Difference between update() and merge()?
- Why does Hibernate auto-update without explicit call?

---

# 8️⃣ Visual Representation

```
new → Transient
        ↓ save()
Persistent
        ↓ session.close()
Detached
        ↓ delete()
Removed
```

---

# 9️⃣ Interview-Level Summary (30 Words)

Hibernate entities move through transient, persistent, detached, and removed states. Persistent objects are tracked via first-level cache and dirty checking, while flush synchronizes changes and commit finalizes transactions.

---