# Sealed Classes and Record Classes

## Why These Features Matter
Modern Java added language features to model domain types more precisely:
- **sealed classes/interfaces** -> restrict inheritance
- **records** -> compact syntax for immutable data carriers

These improve readability, correctness, and domain modeling.

---

## Sealed Classes
A sealed class controls which classes can extend or implement it.

```java
public sealed class Shape permits Circle, Rectangle {
}
```

### Allowed subclasses must be one of:
- `final`
- `sealed`
- `non-sealed`

Example:
```java
final class Circle extends Shape {}
non-sealed class Rectangle extends Shape {}
```

---

## Why Use Sealed Classes?
- close an inheritance hierarchy intentionally
- improve domain safety
- make exhaustive reasoning easier
- better fit for pattern matching and algebraic-style models

### Example use cases
- payment status types
- response variants
- state machine models
- AST/domain modeling

---

## Sealed vs Final vs Abstract
| Feature | Meaning |
|---|---|
| `final` class | no one can extend it |
| `abstract` class | cannot instantiate; inheritance open unless restricted |
| `sealed` class | only explicitly permitted subclasses can extend it |

---

## Records
A record is a compact way to declare a data carrier class.

```java
public record User(Long id, String name) {}
```

The compiler automatically provides:
- private final fields
- canonical constructor
- accessor methods like `id()` and `name()`
- `equals()`
- `hashCode()`
- `toString()`

---

## Why Use Records?
- less boilerplate
- ideal for DTO/value-like models
- semantics focused on data, not mutable behavior
- clearer intent than large POJO boilerplate

---

## Important Record Details
### Records are shallowly immutable
Their components are final, but referenced objects may still be mutable.

```java
record User(List<String> roles) {}
```
This is not deeply immutable unless defensive copying is added.

### Records extend `java.lang.Record`
They cannot extend any other class.

### Records can implement interfaces
Yes, but cannot extend another concrete class.

---

## Customizing Records
You can add:
- compact constructors
- validation logic
- methods
- static members

Example:
```java
public record User(String name) {
    public User {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("name required");
        }
    }
}
```

---

## Sealed + Record Combination
A strong modeling pattern:
```java
sealed interface PaymentResult permits Success, Failure {}
record Success(String txnId) implements PaymentResult {}
record Failure(String reason) implements PaymentResult {}
```

This creates a clean, restricted domain model.

---

## Interview-Specific Questions
### What is a sealed class?
A class or interface that restricts which other classes/interfaces are allowed to extend or implement it.

### What problem do sealed classes solve?
They allow controlled inheritance and make domain hierarchies safer and more explicit.

### What is a record in Java?
A special class designed for immutable-style data carriers with auto-generated constructor, accessors, `equals`, `hashCode`, and `toString`.

### Are records immutable?
They are shallowly immutable. Their components are final, but mutable referenced objects can still change.

### Difference between record and normal POJO?
A record is concise, value-oriented, and auto-generates common boilerplate; a POJO is fully manual and more flexible.

---

## Quick Revision
- sealed -> restrict inheritance.
- record -> concise data carrier.
- records are shallowly immutable.
- sealed hierarchies are excellent for domain modeling.
- sealed + records is a strong interview-worthy combination.

