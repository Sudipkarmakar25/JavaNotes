# Era 4 – Part 2: Core & Advanced JPA Concepts (Highly Important)

---

# 1️⃣ persist() vs merge() (Very Common Interview Question)

## persist()

- Makes transient entity managed
- Used for new objects
- Fails if entity already exists
- Does NOT return entity

```java
User user = new User();
user.setName("Rahul");
em.persist(user);
```

After persist:
→ Entity becomes Managed

---

## merge()

- Used for detached entities
- Returns managed instance
- Can insert or update
- Copies state into persistence context

```java
User detachedUser = new User();
detachedUser.setId(1L);
detachedUser.setName("Amit");

User managed = em.merge(detachedUser);
```

Important:
Always use returned object from merge.

---

# 2️⃣ Entity States (JPA Terminology)

| State | Meaning |
|-------|--------|
| Transient | Not managed |
| Managed | Inside persistence context |
| Detached | Was managed, session closed |
| Removed | Marked for deletion |

Persistence Context manages these transitions.

---

# 3️⃣ Flush vs Commit (JPA Perspective)

## Flush

- Synchronizes persistence context with database
- Executes SQL
- Transaction still active

```java
em.flush();
```

## Commit

- Flushes automatically
- Ends transaction
- Makes changes permanent

```java
em.getTransaction().commit();
```

Flush does NOT commit.

---

# 4️⃣ Dirty Checking in JPA

Managed entities are automatically tracked.

Example:

```java
User user = em.find(User.class, 1L);
user.setName("Amit");
```

No update call required.

On commit:
→ Update SQL generated automatically.

Works only for Managed entities.

---

# 5️⃣ Relationships in JPA

---

## One-to-One

```java
@OneToOne
@JoinColumn(name = "profile_id")
private Profile profile;
```

---

## One-to-Many

```java
@OneToMany(mappedBy = "user")
private List<Order> orders;
```

---

## Many-to-One

```java
@ManyToOne
@JoinColumn(name = "user_id")
private User user;
```

---

## Many-to-Many

```java
@ManyToMany
@JoinTable(
    name = "user_role",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "role_id")
)
private Set<Role> roles;
```

---

# 6️⃣ Fetch Types

Two types:

- EAGER
- LAZY

Default:

- ToOne → EAGER
- ToMany → LAZY

Best practice:
Prefer LAZY.

---

# 7️⃣ Cascade Types

Types:

- PERSIST
- MERGE
- REMOVE
- REFRESH
- DETACH
- ALL

Example:

```java
@OneToMany(cascade = CascadeType.ALL)
```

Saving parent automatically saves children.

---

# 8️⃣ Inheritance Strategies

JPA supports:

## SINGLE_TABLE

All classes in one table.
Fastest.
Uses discriminator column.

## JOINED

Separate tables.
Normalized.
More joins.

## TABLE_PER_CLASS

Separate table per class.
Rarely used.

Example:

```java
@Inheritance(strategy = InheritanceType.JOINED)
```

---

# 9️⃣ Locking in JPA

---

## Optimistic Locking

Uses version column.

```java
@Version
private Long version;
```

Prevents lost updates.
Best for low contention.

---

## Pessimistic Locking

Locks row at database level.

```java
em.find(User.class, 1L, LockModeType.PESSIMISTIC_WRITE);
```

Used when high contention.

---

# 🔟 Why These Concepts Matter

These explain:

- Why merge behaves strangely
- Why update sometimes not saved
- Why LazyInitializationException occurs
- Why N+1 happens
- Why concurrent update fails

Understanding this is critical before Spring Data.

---

# 1️⃣1️⃣ Most Asked Interview Questions

- Difference between persist and merge?
- What is persistence context?
- What is dirty checking?
- Default fetch type?
- Explain inheritance strategies.
- Optimistic vs pessimistic locking?
- What is version column?
- Why does merge return new object?

---

# 1️⃣2️⃣ Interview-Level Summary (30 Words)

JPA manages entity states through persistence context, supports cascading, fetching strategies, inheritance mapping, and locking mechanisms while using dirty checking and transactional synchronization to ensure consistent database operations.

---