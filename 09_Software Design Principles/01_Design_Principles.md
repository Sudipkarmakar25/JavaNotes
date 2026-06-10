# Design Principles

## 1. Why Design Principles Matter
Design principles are practical rules that help teams write code that is easier to:
- understand
- change
- test
- extend
- scale in production

### Why they matter in backend systems
In Java backend projects, business rules change often. Payment flows, auth logic, notification channels, storage providers, and database integrations evolve over time. Good design principles reduce the cost of those changes.

### Production benefits
- **Maintainability** -> bugs are easier to fix
- **Scalability** -> system grows without becoming chaotic
- **Readability** -> new developers understand code faster
- **Extensibility** -> new features can be added with less rewrite
- **Testability** -> services are easier to unit test and mock

### Simple mental model
```text
bad design -> tight coupling -> hard changes -> fragile code
better design -> modular code -> safer changes -> faster delivery
```

---

## 2. SOLID Overview
**SOLID** is a set of five object-oriented design principles:
- **S** -> Single Responsibility Principle
- **O** -> Open Closed Principle
- **L** -> Liskov Substitution Principle
- **I** -> Interface Segregation Principle
- **D** -> Dependency Inversion Principle

> **Interview tip:** SOLID is not about making code abstract everywhere. It is about reducing change impact and coupling.

---

## 3. Single Responsibility Principle (SRP)
### Interview-ready definition
A class should have **one reason to change**, meaning it should handle one primary responsibility.

### Real-world analogy
A restaurant cashier should not also cook food and clean tables.

### Production/backend example
A `UserService` should manage user business logic, while email sending should be in `NotificationService`.

### Bad design
One class handles:
- user creation
- password hashing
- email sending
- audit logging

### Improved design
Split responsibilities:
- `UserService`
- `PasswordEncoder`
- `NotificationService`
- `AuditService`

### Java example
```java
class UserService {
    private final NotificationService notificationService;

    UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    void register(String email) {
        System.out.println("User saved");
        notificationService.send(email);
    }
}

interface NotificationService {
    void send(String email);
}
```

### Common interview questions
- **What does "one reason to change" mean?**
  It means one class should not mix unrelated responsibilities.
- **Is SRP only for classes?**
  No. It also applies to modules, services, and packages.

---

## 4. Open Closed Principle (OCP)
### Interview-ready definition
Software entities should be **open for extension but closed for modification**.

### Real-world analogy
A phone charger socket allows new charger types through adapters without redesigning the wall socket.

### Production/backend example
A payment module should support adding new payment methods without changing stable checkout logic.

### Bad design
```java
class PaymentService {
    void pay(String type) {
        if (type.equals("CARD")) {
            System.out.println("Card payment");
        } else if (type.equals("UPI")) {
            System.out.println("UPI payment");
        }
    }
}
```

Every new type changes old code.

### Improved design
Use abstraction and new implementations.

```java
interface PaymentMethod {
    void pay();
}

class CardPayment implements PaymentMethod {
    public void pay() {
        System.out.println("Card payment");
    }
}

class UpiPayment implements PaymentMethod {
    public void pay() {
        System.out.println("UPI payment");
    }
}
```

### Production use case
- payment gateways
- file storage strategies
- notification channels
- discount strategies

### Common interview questions
- **How is OCP achieved in Java?**
  Using interfaces, inheritance, composition, and strategy-style design.
- **Does OCP mean never changing code?**
  No. It means stable code should not be repeatedly edited for every new variation.

---

## 5. Liskov Substitution Principle (LSP)
### Interview-ready definition
A child class should be usable in place of its parent **without breaking expected behavior**.

### Real-world analogy
If a client rents a "vehicle," replacing a car with another valid vehicle should not break the basic contract of driving behavior.

### Production/backend example
If code depends on `StorageService`, any implementation such as local storage or S3 storage should behave according to the same contract.

### Bad design idea
A subclass weakens or breaks the parent promise.

```java
class Bird {
    void fly() {}
}

class Ostrich extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException();
    }
}
```

### Improved design
Model contracts correctly.

```java
interface Bird {}
interface Flyable {
    void fly();
}

class Sparrow implements Bird, Flyable {
    public void fly() {}
}

class Ostrich implements Bird {}
```

### Backend interpretation
If one repository/service implementation throws unsupported behavior for valid contract methods, LSP is violated.

### Common interview questions
- **How do you detect LSP violation?**
  If replacing implementation breaks logic, assumptions, or invariants.
- **Is LSP only about inheritance?**
  Mostly discussed with inheritance, but practically it is about behavioral substitutability.

---

## 6. Interface Segregation Principle (ISP)
### Interview-ready definition
Clients should not be forced to depend on methods they do not use.

### Real-world analogy
A TV remote should not have airplane cockpit controls.

### Production/backend example
Authentication services should not depend on file-storage methods, and storage services should not depend on login methods.

### Bad design
```java
interface Worker {
    void code();
    void test();
    void deploy();
    void manageTeam();
}
```

Every implementation is forced to support everything.

### Improved design
```java
interface Developer {
    void code();
}

interface Tester {
    void test();
}

interface DevOps {
    void deploy();
}
```

### Production use case
- split large service interfaces
- smaller repository contracts
- dedicated auth, notification, and audit interfaces

### Common interview questions
- **What is a fat interface?**
  An interface containing unrelated methods for many clients.
- **Why is ISP useful in Spring Boot?**
  Small interfaces improve mocking, testing, and modular service design.

---

## 7. Dependency Inversion Principle (DIP)
### Interview-ready definition
High-level modules should depend on **abstractions**, not concrete implementations.

### Real-world analogy
A person uses a switch, not direct wiring details inside the wall.

### Production/backend example
A checkout service should depend on `PaymentGateway`, not directly on `StripePaymentGateway`.

### Bad design
```java
class AuthService {
    private final JwtTokenProvider tokenProvider = new JwtTokenProvider();
}
```

### Improved design
```java
interface TokenProvider {
    String generate();
}

class JwtTokenProvider implements TokenProvider {
    public String generate() {
        return "jwt-token";
    }
}

class AuthService {
    private final TokenProvider tokenProvider;

    AuthService(TokenProvider tokenProvider) {
        this.tokenProvider = tokenProvider;
    }
}
```

### Why important in production
DIP enables:
- loose coupling
- easier unit testing
- easier mocking
- easier implementation replacement
- better Spring dependency injection

### Common interview questions
- **Real-time use of DIP?**
  Service layer depending on repository interfaces, payment interfaces, storage interfaces, or auth provider interfaces.
- **Is DIP same as dependency injection?**
  No. DI is a technique; DIP is the principle behind depending on abstractions.

---

## 8. Practical SOLID Examples in Backend Systems

| Use case | Principle most visible | Example |
|---|---|---|
| Notification Service | SRP, OCP | email, SMS, push as separate implementations |
| Payment System | OCP, DIP | add Razorpay/Stripe without changing checkout flow |
| File Storage | LSP, DIP | local, S3, Azure implementations behind one contract |
| Authentication Service | ISP, DIP | token provider separated from user service |
| Database Layer | SRP, DIP | repository abstraction separates persistence from service logic |

---

## 9. DRY Principle
### Definition
**DRY = Don't Repeat Yourself**.

The same business logic, rule, or knowledge should not be copied in multiple places.

### Why duplication is harmful
- bug fixes must be repeated
- behavior becomes inconsistent
- code becomes harder to maintain
- validation rules drift across services

### Bad example
```java
class UserValidator {
    boolean isValid(String email) {
        return email != null && email.contains("@");
    }
}

class AdminValidator {
    boolean isValid(String email) {
        return email != null && email.contains("@");
    }
}
```

### Better design
```java
class EmailValidator {
    boolean isValid(String email) {
        return email != null && email.contains("@");
    }
}
```

### Production example
Shared validation, exception mapping, DTO conversion, and audit logging are common DRY candidates.

### Interview note
DRY is about avoiding duplicated **knowledge**, not forcing every similar line into abstraction.

---

## 10. KISS Principle
### Definition
**KISS = Keep It Simple, Stupid**.

Prefer the simplest design that correctly solves the current problem.

### Why it matters
- easier debugging
- easier onboarding
- lower cognitive load
- fewer hidden bugs

### Complex vs simple example
Bad: build 7 abstraction layers for one simple file upload flow.

Better:
- controller
- service
- storage interface
- one implementation initially

### Production example
For a small internal tool, a clean monolith with simple services is often better than introducing events, CQRS, and multiple microservices too early.

### Interview note
Simple design is not weak design. Good simplicity often scales better because it is understandable.

---

## 11. YAGNI Principle
### Definition
**YAGNI = You Aren't Gonna Need It**.

Do not build features, abstractions, or optimizations before they are actually needed.

### Why it matters
- reduces wasted effort
- avoids unnecessary abstraction
- keeps product iteration fast
- lowers maintenance cost

### Startup/product example
Do not build plugin architecture, 5 payment gateways, and multi-region workflows before first real customer need.

### Microservice overengineering example
A team with one product and small traffic often starts better with a modular monolith than 12 microservices.

### Interview note
YAGNI protects teams from premature abstraction and premature optimization.

---

## 12. Important Relationships

### SOLID vs Design Patterns
- **SOLID** = design principles
- **Design Patterns** = reusable solutions to recurring problems
- patterns work better when guided by SOLID

### DRY vs Over-Abstraction
Too much DRY can create bad abstractions.

If two pieces of code look similar today but evolve differently tomorrow, forcing them into one abstraction can be harmful.

### KISS vs Scalability
Simple design does not oppose scalability.

The best path is usually:
```text
start simple -> measure bottlenecks -> evolve carefully
```

### YAGNI in startups and agile teams
Agile teams benefit from building only what current requirements justify, then refactoring when real use cases appear.

---

## 13. Common Interview Questions
### Which SOLID principle is most important?
There is no universal winner, but **SRP** and **DIP** are the most visible in enterprise/backend systems because they reduce coupling and simplify change.

### Real-time use of DIP?
Service depending on repository interface, payment gateway interface, storage interface, or token provider interface.

### Why is overengineering harmful?
It increases complexity, slows delivery, makes debugging harder, and creates abstractions nobody really needs.

### Difference between DRY and KISS?
- **DRY** focuses on avoiding duplicated knowledge
- **KISS** focuses on keeping design simple

A design can be DRY but still too complex.

### Why does YAGNI matter in production?
Because unnecessary features and abstractions still have maintenance, testing, and debugging cost.

---

## 14. Best Practices
- write modular classes and services
- keep services focused on one responsibility
- prefer composition over inheritance
- avoid tight coupling to concrete classes
- code for extension where variation is expected
- use interfaces where multiple implementations are realistic
- do not abstract too early
- keep designs simple and readable
- validate design choices using real production change scenarios

---

## 15. Quick Revision
| Principle | One-line takeaway |
|---|---|
| SRP | one unit should have one main reason to change |
| OCP | extend behavior without repeatedly editing stable code |
| LSP | child implementations must preserve parent contract |
| ISP | do not force clients to depend on unused methods |
| DIP | depend on abstractions, not concrete implementations |
| DRY | avoid duplicated business knowledge |
| KISS | prefer simplest correct solution |
| YAGNI | do not build what is not needed yet |

> **Final interview tip:** In Java backend interviews, explain every principle using a service/repository/payment/storage example, not only textbook definitions.

