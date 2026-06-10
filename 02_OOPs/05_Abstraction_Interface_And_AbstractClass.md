# Abstraction, Interface and Abstract Class

## 1. What is Abstraction?
**Abstraction** means exposing only essential behavior while hiding unnecessary implementation details.

### Simple idea
Focus on **what an object does**, not **how it does it internally**.

### Real-world analogy
Using a car means dealing with `start()`, `accelerate()`, and `brake()`, not piston timing or gearbox internals.

### Production use case
In backend systems, controllers call methods like `processPayment()` without knowing gateway integration, retry logic, or audit details.

---

## 2. Why Abstraction is Needed
Abstraction helps:
- reduce complexity
- hide implementation details
- improve maintainability
- support loose coupling
- allow interchangeable implementations
- simplify testing and extension

### Key point
Developers work with contracts and behavior instead of depending on internal implementation.

---

## 3. Real-life Examples of Abstraction
- **ATM** -> withdraw/deposit, hides banking internals
- **Remote control** -> buttons, hides circuitry
- **Payment service** -> `pay()`, hides gateway logic
- **Repository interface** -> `save()` / `findById()`, hides database details

---

## 4. Abstract Class
An **abstract class** cannot be instantiated directly and may contain:
- abstract methods
- concrete methods
- fields
- constructors

### Syntax
```java
abstract class PaymentService {
    abstract void pay(double amount);

    void logPayment() {
        System.out.println("Payment logged");
    }
}
```

### Example
```java
class CardPaymentService extends PaymentService {
    @Override
    void pay(double amount) {
        System.out.println("Paid by card: " + amount);
    }
}
```

### Internal working
An abstract class provides partial implementation. Subclasses inherit common logic and must implement required abstract methods.

### Production use case
Useful when related classes share state or reusable logic, such as base services, template processors, or framework abstractions.

---

## 5. Interface
An **interface** defines a contract that implementing classes must follow.

### Syntax
```java
interface NotificationService {
    void send(String message);
}
```

### Example
```java
class EmailNotificationService implements NotificationService {
    @Override
    public void send(String message) {
        System.out.println("Email sent: " + message);
    }
}
```

### Internal working
An interface defines required behavior rather than shared object state.

### Production use case
Used heavily in Spring Boot for services, repositories, clients, strategies, adapters, and extension points.

---

## 6. Abstract Class vs Interface

| Aspect | Abstract Class | Interface |
|---|---|---|
| Purpose | partial implementation + shared base | behavior contract |
| Multiple inheritance | no | yes, via multiple interfaces |
| Fields | instance fields allowed | constants only by default |
| Constructors | allowed | not allowed |
| State sharing | strong | minimal |
| Best use | closely related classes | loose coupling and contracts |

Use abstract class when classes share common state/logic; use interface when you want flexible contracts and loose coupling.

---

## 7. Multiple Inheritance Using Interfaces
Java does not allow multiple inheritance of classes, but it allows one class to implement multiple interfaces.

### Example
```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    public void fly() {}
    public void swim() {}
}
```

### Why useful
It combines multiple capabilities without class hierarchy ambiguity.

---

## 8. Functional Interface Basics
A **functional interface** has exactly one abstract method.

### Example
```java
@FunctionalInterface
interface Calculator {
    int operate(int a, int b);
}
```

### Why important
Functional interfaces power:
- lambda expressions
- method references
- Stream API
- lightweight strategy-style abstractions

### Production use case
Common in modern Java APIs and callback-based code.

---

## 9. Marker Interface
A **marker interface** has no methods and is used only to mark a class with special meaning.

### Examples
- `Serializable`
- `Cloneable`

### Internal meaning
The JVM or framework checks for the marker and changes behavior accordingly.

---

## 10. `default` Methods
Since Java 8, interfaces can contain `default` methods with implementation.

### Example
```java
interface Logger {
    default void log(String msg) {
        System.out.println(msg);
    }
}
```

### Why added
To evolve interfaces without breaking all existing implementations.

### Production use case
Useful in framework APIs where optional common behavior is added later.

---

## 11. `static` Methods in Interfaces
Interfaces can also define `static` utility methods.

### Example
```java
interface MathUtil {
    static int square(int x) {
        return x * x;
    }
}
```

### Use case
Utility logic closely related to interface meaning.

---

## 12. Loose Coupling
**Loose coupling** means components depend on abstractions, not implementation details.

### Example
```java
interface PaymentService {
    void pay();
}

class CheckoutService {
    private final PaymentService paymentService;

    CheckoutService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### Benefit
Implementation can change without changing the consumer.

---

## 13. Dependency Inversion Basics
The **Dependency Inversion Principle** says high-level modules should depend on abstractions, not concrete classes.

### Bad design
```java
class CheckoutService {
    private StripePaymentService service = new StripePaymentService();
}
```

### Better design
```java
class CheckoutService {
    private final PaymentService service;

    CheckoutService(PaymentService service) {
        this.service = service;
    }
}
```

### Production use case
This is a key reason interfaces are common in enterprise Java design.

---

## 14. Interface-based Programming
Interface-based programming means coding against contracts instead of implementations.

### Example
```java
List<String> names = new ArrayList<>();
```

You code to `List`, not `ArrayList`.

### Benefit
Implementation can be swapped later with minimal impact.

---

## 15. Why Interfaces are Heavily Used in Spring Framework
Spring uses interfaces because they support:
- loose coupling
- easy testing and mocking
- proxy-based features
- interchangeable implementations
- AOP and transaction wrappers

### Common areas
- service layer contracts
- JPA repositories
- security interfaces
- filters, handlers, interceptors

### Key point
Interfaces make dependency injection and framework enhancement easier.

---

## 16. JVM Implementation Basics of Interfaces
### Conceptually
When code calls a method through an interface reference, JVM must find the actual implementation class at runtime.

### Example
```java
class Demo {
    void test() {
        PaymentService p = new CardPaymentService();
        p.pay();
    }
}
```

### Internal working
- compiler verifies `pay()` exists in the interface
- runtime checks actual object type
- JVM dispatches to the implementing class method

### Interview-safe wording
Interface calls are resolved at runtime based on the actual implementation object, similar in spirit to dynamic dispatch for overriding.

---

## 17. Strategy Pattern Basics
The **Strategy Pattern** uses interchangeable implementations behind a common interface.

### Example
```java
interface DiscountStrategy {
    double apply(double price);
}

class NoDiscount implements DiscountStrategy {
    public double apply(double price) { return price; }
}

class FestivalDiscount implements DiscountStrategy {
    public double apply(double price) { return price * 0.8; }
}
```

### Production use case
Useful for payment selection, pricing rules, notifications, retry policies, and authentication strategies.

---

## 18. Dependency Injection Relation with Interfaces
DI and abstraction work naturally together.

### Example
```java
@Service
class CheckoutService {
    private final PaymentService paymentService;

    CheckoutService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### Why powerful
- easier testing
- easy replacement of implementations
- lower coupling
- cleaner architecture

---

## 19. Service Abstraction Examples
### Example
```java
interface UserService {
    void registerUser(String name);
}

class UserServiceImpl implements UserService {
    public void registerUser(String name) {
        System.out.println("User registered: " + name);
    }
}
```

### Production meaning
Controllers depend on `UserService`, not its internal implementation.

---

## 20. Real-life Production Examples
- `PaymentService` -> Stripe / PayPal / Mock
- `NotificationService` -> Email / SMS / Push
- `StorageService` -> Local / S3 / Azure Blob
- `AuthProvider` -> JWT / OAuth / LDAP
- `DiscountStrategy` -> different promotion rules

These are common backend patterns where abstraction improves maintainability.

---

## 21. Common Mistakes
- using interfaces everywhere even when no real abstraction exists
- using abstract class when a loose contract would be better
- confusing encapsulation with abstraction
- creating unnecessary layers
- exposing implementation-specific details in interfaces
- tightly coupling service code to concrete classes

---

## 22. Best Practices
- code to interfaces when multiple implementations are possible
- use abstract class when shared state/logic truly exists
- keep interfaces small and focused
- avoid fat interfaces
- prefer constructor injection in Spring Boot
- use strategy pattern where behavior varies
- hide implementation details behind clean contracts
- avoid abstraction without real benefit
