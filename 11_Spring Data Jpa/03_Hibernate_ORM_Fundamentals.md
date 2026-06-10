# Era 3 – Hibernate (ORM Revolution)

---

# 1️⃣ Introduction to Hibernate

Hibernate is an **Object-Relational Mapping (ORM) framework** for Java.

It solves the problem of mapping:

Database Tables ↔ Java Objects

Instead of writing SQL manually, developers interact with **Java objects**, and Hibernate automatically generates SQL behind the scenes.

---

# 2️⃣ What is ORM?

ORM (Object Relational Mapping) is a technique that:

- Maps a database table to a Java class
- Maps table rows to objects
- Maps columns to fields
- Handles SQL automatically

Example mapping:

| Database Table | Java Class |
|---------------|------------|
| users         | User       |
| id column     | id field   |
| name column   | name field |

ORM reduces:

- Manual SQL writing
- ResultSet mapping
- Boilerplate persistence code

---

# 3️⃣ Why Hibernate Was Revolutionary

Before Hibernate:

- Developers wrote SQL manually
- Manual object mapping required
- Relationships handled manually

Hibernate introduced:

- Automatic CRUD operations
- Object-based querying (HQL)
- Caching
- Lazy loading
- Dirty checking
- Transaction integration

It made persistence **object-oriented**.

---

# 4️⃣ Hibernate Architecture

Core components:

1. Configuration
2. SessionFactory
3. Session
4. Transaction
5. Query
6. Entity

Architecture flow:

```
Application
     ↓
Session
     ↓
Hibernate
     ↓
JDBC
     ↓
Database
```

Hibernate sits between application and JDBC.

---

# 5️⃣ Core Components Explained

## Configuration

Loads:
- Database details
- Dialect
- Entity mappings

Example:

```java
Configuration configuration = new Configuration();
configuration.configure("hibernate.cfg.xml");
configuration.addAnnotatedClass(User.class);
```

---

## SessionFactory

- Heavyweight object
- Created once per application
- Thread-safe
- Responsible for creating Sessions

```java
SessionFactory sessionFactory = configuration.buildSessionFactory();
```

---

## Session

- Lightweight object
- Not thread-safe
- Represents a single unit of work
- Similar to JDBC Connection

```java
Session session = sessionFactory.openSession();
```

---

## Transaction

Ensures atomic operations.

```java
Transaction tx = session.beginTransaction();
tx.commit();
```

---

# 6️⃣ Basic CRUD Example

## Step 1 – Entity Class

```java
import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    // getters and setters
}
```

---

## Step 2 – Save Entity

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

User user = new User();
user.setName("Rahul");

session.save(user);

tx.commit();
session.close();
```

Hibernate generates SQL automatically:

```sql
INSERT INTO users (name) VALUES ('Rahul');
```

---

## Step 3 – Fetch Entity

```java
Session session = sessionFactory.openSession();

User user = session.get(User.class, 1L);

session.close();
```

Generated SQL:

```sql
SELECT * FROM users WHERE id = 1;
```

---

# 7️⃣ Advantages of Hibernate

- Reduces boilerplate
- Automatic SQL generation
- Object-oriented persistence
- Caching support
- Database independence
- HQL support

---

# 8️⃣ Limitations of Hibernate

- Learning curve
- Hidden SQL (harder debugging)
- Performance tuning required
- Hibernate-specific API (not standard)

This limitation led to:

→ JPA (standardization layer)

---

# 9️⃣ Important Interview Concepts Introduced in Hibernate

Hibernate introduced concepts that are heavily asked:

- Entity lifecycle states
- First-level cache
- Dirty checking
- Lazy vs eager loading
- N+1 problem
- Flush vs commit
- Cascading
- Fetch strategies

These will be covered in the next parts.

---

# 🔟 Evolution So Far

```
JDBC (manual SQL + mapping)
        ↓
Spring JDBC (less boilerplate)
        ↓
Hibernate (ORM abstraction)
```

---

# 1️⃣1️⃣ Interview-Level Summary (30 Words)

Hibernate is an ORM framework that maps Java objects to database tables, automates SQL generation, manages sessions and transactions, introduces caching and entity lifecycle management, simplifying persistence development significantly.

---