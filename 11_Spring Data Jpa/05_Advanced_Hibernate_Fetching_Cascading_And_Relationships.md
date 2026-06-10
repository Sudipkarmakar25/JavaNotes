# Era 3 – Part 3: Advanced Hibernate Concepts (Highly Asked in Interviews)

---

# 1️⃣ Lazy vs Eager Loading

Hibernate supports two fetch strategies:

- EAGER
- LAZY

These control when related entities are loaded.

---

## EAGER Fetching

Data is loaded immediately.

Example:

```java
@ManyToOne(fetch = FetchType.EAGER)
private Department department;
```

When you fetch Employee:
→ Department is also fetched immediately.

Problem:
- Can cause performance issues
- Can load unnecessary data

---

## LAZY Fetching (Default for Collections)

Data is loaded only when accessed.

```java
@OneToMany(fetch = FetchType.LAZY)
private List<Order> orders;
```

Orders are NOT fetched until:

```java
employee.getOrders().size();
```

Advantage:
- Better performance
- Loads data only when needed

---

# 2️⃣ LazyInitializationException (Common Interview Question)

Occurs when:

- Entity is detached
- Lazy field accessed outside session

Example:

```java
session.close();
employee.getOrders(); // Exception
```

Why?

Because Hibernate needs active session to fetch lazy data.

Solutions:
- Use JOIN FETCH
- Use Open Session in View
- Convert to DTO inside transaction

---

# 3️⃣ N+1 Problem (Very Important)

Problem:

1 query to fetch parent
+ N queries to fetch children

Example:

```java
List<Employee> employees = session.createQuery("FROM Employee").list();
```

Then inside loop:

```java
employee.getDepartment().getName();
```

SQL Generated:

1 query for employees  
N queries for departments

Total = N + 1 queries

This kills performance.

---

## Fix Using JOIN FETCH

```java
SELECT e FROM Employee e JOIN FETCH e.department
```

Now:
- Single SQL query
- No extra queries

---

# 4️⃣ Cascading

Cascade defines what happens to related entities.

Example:

```java
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
private List<Order> orders;
```

Cascade types:

- PERSIST
- MERGE
- REMOVE
- REFRESH
- DETACH
- ALL

Example:

If user is saved:
→ Orders are automatically saved.

---

# 5️⃣ Orphan Removal

Removes child entity if removed from collection.

```java
@OneToMany(orphanRemoval = true)
```

If:

```java
user.getOrders().remove(order);
```

Order will be deleted from database automatically.

---

# 6️⃣ HQL (Hibernate Query Language)

HQL is object-oriented query language.

Instead of:

```sql
SELECT * FROM users;
```

We write:

```java
FROM User
```

Example:

```java
Query<User> query = session.createQuery(
    "FROM User WHERE name = :name", User.class);
query.setParameter("name", "Rahul");
```

HQL works with:
- Class names
- Field names

Not table names.

---

# 7️⃣ Native SQL Query

Hibernate also allows native SQL.

```java
session.createNativeQuery("SELECT * FROM users", User.class);
```

Used when:
- Complex queries
- DB-specific features
- Performance tuning

---

# 8️⃣ Fetch Strategies (Deep Concept)

Hibernate supports:

- Select fetching (default)
- Join fetching
- Batch fetching
- Subselect fetching

Join fetching is most important for interviews.

---

# 9️⃣ Performance Considerations

Common performance issues:

- N+1 problem
- Too many eager relationships
- Large session memory
- Missing indexes

Best practice:
- Prefer LAZY
- Use JOIN FETCH when required
- Keep session short-lived

---

# 🔟 Evolution Understanding

Hibernate introduced:

- Automatic relationship handling
- Fetch strategies
- Caching
- ORM abstraction

But it was still vendor-specific.

This led to:

→ JPA (standard ORM specification)

---

# 1️⃣1️⃣ Most Asked Interview Questions From This Part

- Difference between LAZY and EAGER?
- What is N+1 problem?
- How to fix N+1?
- What is cascade?
- What is orphanRemoval?
- What is HQL?
- Difference between HQL and SQL?
- Why LazyInitializationException occurs?

---

# 1️⃣2️⃣ Interview-Level Summary (30 Words)

Hibernate supports lazy and eager fetching, cascading operations, HQL queries, and fetch strategies. Understanding N+1 problems, join fetch, and session behavior is critical for performance and interview success.

---