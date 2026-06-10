# Encapsulation

## 1. What is Encapsulation?
**Encapsulation** is the OOP principle of bundling **data + behavior** together and controlling how object state is accessed or modified.

In Java, it is usually achieved using:
- `private` fields
- controlled methods (`getters`, `setters`, domain methods)
- validation before state change

### Simple idea
Hide internal representation and expose only safe operations.

### Syntax
```java
class BankAccount {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
}
```

### Internal working
Outside code cannot directly modify private state. All changes go through methods, where rules and validation can be enforced.

### Production use case
Backend entities, DTOs, services, and configs often restrict direct state access to avoid invalid data.

---

## 2. Why Encapsulation is Needed
Without encapsulation, any code can freely change object state, leading to:
- invalid data
- hard-to-debug bugs
- tight coupling
- weaker maintainability
- broken business rules

### Example problem
```java
class Account {
    public double balance;
}
```
Any code can do:
```java
class Demo {
    void test(Account account) {
        account.balance = -10000;
    }
}
```

### Encapsulated version
```java
class Account {
    private double balance;

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }
}
```

### Production use case
A `User` object should not allow uncontrolled updates such as `age = -5` or `status = null`.

---

## 3. Data Hiding
**Data hiding** means preventing direct access to internal object state.

### Relation
- **Data hiding** is the mechanism
- **Encapsulation** is the broader design principle

### Example
```java
class User {
    private String password;
}
```
External code cannot access `password` directly.

### Internal working
The class decides what stays hidden and what is exposed, protecting invariants.

### Production use case
Sensitive fields like passwords, tokens, internal IDs, retry counters, and flags should not be directly modifiable.

---

## 4. Getter and Setter Methods
Getters and setters provide controlled access.

### Example
```java
class Employee {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        if (name != null && !name.isBlank()) {
            this.name = name;
        }
    }
}
```

### Important point
Encapsulation does **not** mean generating getters and setters for everything. Unrestricted setters often weaken design.

### Better design
Expose domain-specific methods where possible:
```java
class Order {
    private String status;

    public void markAsPaid() {
        this.status = "PAID";
    }
}
```

### Production use case
Enterprise code often prefers validated setters or domain methods over raw field mutation.

---

## 5. Encapsulation vs Abstraction

| Aspect | Encapsulation | Abstraction |
|---|---|---|
| Focus | protect internal state | hide implementation complexity |
| Goal | controlled access | expose essential behavior |
| Achieved by | access modifiers, methods | abstract classes, interfaces |
| Example | private field + setter validation | `PaymentService` interface |

Encapsulation protects **how data is used**; abstraction hides **how behavior is implemented**.

---

## 6. Advantages of Encapsulation
- protects object state
- enforces validation
- improves maintainability
- reduces coupling
- improves testability
- supports domain modeling
- prevents misuse of internal fields

### Production angle
Internal changes stay localized and do not force wide code changes outside the class.

---

## 7. Real-life Examples of Encapsulation
- **ATM / BankAccount** -> you use deposit/withdraw, not direct balance update
- **Car** -> you use accelerator/brake, not engine internals
- **Spring service** -> controller should call service methods, not modify repository/entity internals directly

---

## 8. Immutable Class Basics
An **immutable class** is a stronger form of encapsulation where state cannot change after construction.

### Example
```java
final class UserDto {
    private final String name;

    UserDto(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

### Why related
Immutability protects state by removing mutation completely.

### Production use case
DTOs, value objects, config snapshots, and response objects are common immutable candidates.

---

## 9. Defensive Copying Basics
If an object contains mutable fields like `Date`, `List`, or arrays, returning them directly breaks encapsulation.

### Problem
```java
class Employee {
    private Date joiningDate;

    public Date getJoiningDate() {
        return joiningDate;
    }
}
```

### Better
```java
class Employee {
    private Date joiningDate;

    public Date getJoiningDate() {
        return new Date(joiningDate.getTime());
    }
}
```

### Internal working
Defensive copying prevents outside code from mutating internal state indirectly.

### Production use case
Important when exposing mutable dates, collections, or arrays from domain objects.

---

## 10. Read-only Objects
A read-only object exposes getters but no state-changing API.

### Example
```java
class Config {
    private final String url;

    Config(String url) {
        this.url = url;
    }

    public String getUrl() {
        return url;
    }
}
```

### Difference from immutable
Read-only API does not guarantee deep immutability if mutable internals are exposed.

---

## 11. Tight Coupling vs Loose Coupling
### Tight coupling
One class depends heavily on another class’s internals.

### Loose coupling
One class depends only on exposed contract or behavior.

### Bad design
```java
class ReportService {
    UserRepository repo = new UserRepository();
}
```

### Better design
```java
class ReportService {
    private final UserRepository repo;

    ReportService(UserRepository repo) {
        this.repo = repo;
    }
}
```

### Relation to encapsulation
Encapsulation reduces dependency on internal details and supports loose coupling.

---

## 12. Dependency Injection Basics
**Dependency Injection (DI)** means providing dependencies from outside instead of creating them inside the class.

### Why it supports encapsulation
The class hides internal logic while collaborators are injected through constructor, setter, or framework wiring.

### Spring Boot example
```java
@Service
class UserService {
    private final UserRepository repository;

    UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### Production use case
DI improves testability, loose coupling, and clean architecture.

---

## 13. Encapsulation in Enterprise Applications
In enterprise systems, encapsulation appears in:
- service-layer APIs
- domain objects
- DTOs
- repository boundaries
- configuration classes

### Example
A controller should call `orderService.placeOrder()` instead of changing multiple internal entity fields directly.

### Benefit
Business rules stay centralized and protected.

---

## 14. Encapsulation in Microservices
At microservice level, encapsulation also applies to service boundaries.

A microservice should expose only required contracts:
- REST endpoints
- events/messages
- DTO schemas

Internal database schema, business logic, and helper flows should remain hidden.

### Production meaning
Good microservices encapsulate internal implementation behind stable APIs.

---

## 15. Common Mistakes
- making fields `public`
- generating getters/setters blindly
- exposing mutable collections directly
- skipping validation in setters/domain methods
- letting controllers manipulate domain state directly
- confusing encapsulation with only "making everything private"

---

## 16. Best Practices
- keep fields `private`
- expose only necessary methods
- prefer domain-specific methods over generic setters
- validate state transitions
- use immutability where practical
- use defensive copying for mutable fields
- keep service/repository/controller boundaries clean
- prefer constructor injection in Spring Boot
