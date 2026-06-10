# Era 3 – Part 4: Hibernate Performance, Caching & Internal Mechanics

---

# 1️⃣ First-Level Cache (Session Cache) – Recap

- Scoped to Session
- Enabled by default
- Cannot be disabled
- Stores entities by primary key
- Prevents duplicate queries in same session

Example:

```java
User u1 = session.get(User.class, 1L);
User u2 = session.get(User.class, 1L);
```

Second call:
→ No SQL executed  
→ Returned from session cache

Important:
Cache key = Entity class + ID

---

# 2️⃣ Second-Level Cache (Advanced Interview Topic)

Unlike first-level cache:

- Shared across sessions
- Optional
- Requires configuration
- Uses providers like:
    - Ehcache
    - Redis
    - Infinispan

Flow:

Session → First-level cache  
If miss → Check Second-level cache  
If miss → Query DB

---

## Enabling Second-Level Cache

Example configuration:

```properties
hibernate.cache.use_second_level_cache=true
hibernate.cache.region.factory_class=org.hibernate.cache.jcache.JCacheRegionFactory
```

Entity-level enable:

```java
@Cacheable
@org.hibernate.annotations.Cache(
    usage = CacheConcurrencyStrategy.READ_WRITE
)
@Entity
public class User {
}
```

---

# 3️⃣ Query Cache

Stores result of query.

Must enable:

```properties
hibernate.cache.use_query_cache=true
```

Used carefully because:
- Can cause stale data
- Needs proper invalidation

---

# 4️⃣ Flush Modes

Flush synchronizes session state with database.

Flush modes:

- AUTO (default)
- COMMIT
- MANUAL

Example:

```java
session.setHibernateFlushMode(FlushMode.COMMIT);
```

AUTO:
Flush before query execution.

COMMIT:
Flush only during commit.

---

# 5️⃣ Batching (Performance Optimization)

Instead of multiple insert statements:

```sql
INSERT INTO users ...
INSERT INTO users ...
INSERT INTO users ...
```

Hibernate can batch them.

Enable batching:

```properties
hibernate.jdbc.batch_size=20
```

Improves bulk insert/update performance.

---

# 6️⃣ Fetch Size & JDBC Optimization

```properties
hibernate.jdbc.fetch_size=50
```

Reduces memory pressure during large result processing.

---

# 7️⃣ Transaction Boundaries & Performance

Best practices:

- Keep transactions short
- Avoid remote API calls inside transaction
- Avoid long-running sessions
- Avoid loading large collections unnecessarily

---

# 8️⃣ Common Performance Mistakes

- EAGER relationships everywhere
- Not fixing N+1
- No indexes in DB
- Huge session scope
- No batching
- Using too many bidirectional relationships

---

# 9️⃣ Hibernate vs JDBC Performance

Hibernate:
- Slight abstraction overhead
- Huge productivity gain
- Built-in caching
- Dirty checking

JDBC:
- Faster for simple raw operations
- Full manual control

In most enterprise systems:
Hibernate performance is sufficient when tuned properly.

---

# 🔟 Why JPA Was Needed

Problem with Hibernate:

- Vendor-specific API
- No standard
- Switching ORM was difficult

Solution:

→ JPA (Java Persistence API)  
A standard specification for ORM.

Hibernate became:
One implementation of JPA.

---

# 1️⃣1️⃣ Hibernate Complete Evolution Summary

```
JDBC → Manual SQL
Spring JDBC → Reduced boilerplate
Hibernate → ORM + Advanced features
JPA → Standardized ORM
Spring Data JPA → Automated repositories
```

---

# 1️⃣2️⃣ Most Asked Interview Questions From This Section

- Difference between first-level and second-level cache?
- What is query cache?
- What is flush mode?
- How to improve Hibernate performance?
- How batching works?
- Why Hibernate sometimes slow?
- How to avoid memory leak in session?

---

# 1️⃣3️⃣ Interview-Level Summary (30 Words)

Hibernate improves performance using first-level cache, optional second-level cache, batching, and optimized flush strategies. Understanding caching layers and session scope is critical for building scalable enterprise applications.

---