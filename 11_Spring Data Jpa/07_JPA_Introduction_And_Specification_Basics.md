# Era 4 – JPA (Java Persistence API)

---

# 1️⃣ What is JPA?

JPA (Java Persistence API) is a **specification** for Object Relational Mapping in Java.

Important:

JPA is NOT an implementation.

It only defines:

- Interfaces
- Rules
- Annotations
- Standards

Implementations include:

- Hibernate
- EclipseLink
- OpenJPA

Hibernate became one implementation of JPA.

---

# 2️⃣ Why JPA Was Created

Problem with Hibernate:

- Vendor-specific API
- Hard to switch ORM provider
- No standardization

Solution:

JPA standardized ORM behavior.

Now developers code against JPA interfaces, not Hibernate classes.

This ensures:

- Portability
- Cleaner architecture
- Industry standardization

---

# 3️⃣ JPA Architecture

Core components:

1. Entity
2. EntityManager
3. Persistence Context
4. EntityManagerFactory
5. Transaction

Flow:

Application
↓
EntityManager
↓
Persistence Context
↓
JPA Provider (Hibernate)
↓
JDBC
↓
Database

---

# 4️⃣ Core Components Explained

---

## Entity

A simple POJO annotated with `@Entity`.

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

## EntityManagerFactory

- Heavyweight object
- Created once per application
- Thread-safe
- Creates EntityManager

```java
EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("my-persistence-unit");
```

---

## EntityManager

- Similar to Hibernate Session
- Not thread-safe
- Manages entity lifecycle
- Performs CRUD operations

```java
EntityManager em = emf.createEntityManager();
```

---

## Persistence Context

- First-level cache in JPA
- Tracks entity states
- Ensures identity guarantee

Important:

One entity instance per ID per persistence context.

---

# 5️⃣ Basic CRUD Using JPA

---

## Persist (Insert)

```java
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

User user = new User();
user.setName("Rahul");

em.persist(user);

em.getTransaction().commit();
em.close();
```

---

## Find (Select)

```java
User user = em.find(User.class, 1L);
```

---

## Update (Dirty Checking)

```java
User user = em.find(User.class, 1L);
user.setName("Amit");
```

No explicit update call needed.

On commit:
Hibernate generates update SQL.

---

## Remove (Delete)

```java
User user = em.find(User.class, 1L);
em.remove(user);
```

---

# 6️⃣ Entity States in JPA (Same Concept as Hibernate)

- Transient
- Managed (Persistent)
- Detached
- Removed

But JPA terminology:

Persistent = Managed

Important:

EntityManager manages state transitions.

---

# 7️⃣ Difference: Hibernate Session vs JPA EntityManager

| Hibernate | JPA |
|-----------|-----|
| Session | EntityManager |
| SessionFactory | EntityManagerFactory |
| HQL | JPQL |
| Vendor-specific | Standard API |

JPA is abstraction over Hibernate.

---

# 8️⃣ JPQL (Java Persistence Query Language)

JPQL works with:

- Entity names
- Field names

Example:

```java
TypedQuery<User> query =
    em.createQuery("SELECT u FROM User u WHERE u.name = :name", User.class);

query.setParameter("name", "Rahul");

List<User> users = query.getResultList();
```

JPQL is database-independent.

---

# 9️⃣ Why JPA Matters for Spring

Spring Data JPA is built on:

JPA interfaces.

Without understanding JPA:

Spring Data becomes confusing.

Understanding:

- Persistence Context
- Dirty checking
- Flush behavior
- Lazy loading

Is critical before Spring Data.

---

# 🔟 Evolution Now

```
JDBC
   ↓
Spring JDBC
   ↓
Hibernate (ORM)
   ↓
JPA (Standard ORM Spec)
```

Next:

→ Spring Data JPA (Automation Layer)

---

# 1️⃣1️⃣ Most Asked Interview Questions

- What is JPA?
- Is JPA an implementation?
- Difference between Hibernate and JPA?
- What is EntityManager?
- What is Persistence Context?
- Difference between persist() and merge()?
- What is JPQL?

---

# 1️⃣2️⃣ Interview-Level Summary (30 Words)

JPA is a standardized ORM specification that defines entity management, persistence context, and query mechanisms, allowing portable database interaction through implementations like Hibernate while abstracting vendor-specific behavior.

---