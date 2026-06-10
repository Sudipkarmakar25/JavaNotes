# Immutable Class in Java

## What Is an Immutable Class?
An immutable class is a class whose objects cannot change state after creation.

Examples from JDK:
- `String`
- wrapper classes
- many classes in `java.time`

---

## Why Immutability Matters
- naturally thread-safe
- safe to share across methods/threads
- good as cache keys and map keys
- easier reasoning and debugging
- avoids accidental side effects

---

## Rules to Create an Immutable Class
### 1. Make class final
Prevents subclass from adding mutability.

### 2. Make fields private and final
Prevents reassignment and external direct access.

### 3. Do not provide setters
No state mutation API.

### 4. Initialize all fields in constructor
Object should be fully built when created.

### 5. Defensive copy mutable inputs and outputs
Most important rule when fields are mutable objects.

---

## Example
```java
public final class Employee {
    private final int id;
    private final String name;
    private final Date joiningDate;

    public Employee(int id, String name, Date joiningDate) {
        this.id = id;
        this.name = name;
        this.joiningDate = new Date(joiningDate.getTime());
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public Date getJoiningDate() {
        return new Date(joiningDate.getTime());
    }
}
```

Without defensive copy, callers could mutate internal state.

---

## Why `final` Alone Is Not Enough
```java
final class User {
    private final List<String> roles;
}
```

This is not automatically immutable if `roles` can be modified from outside.

Need either:
- defensive copy
- unmodifiable view + internal safety
- immutable collection implementation

---

## Deep Interview Point: Safe Publication
Immutable objects are much easier to publish safely between threads because their state does not change after construction.
Combined with final fields, this reduces visibility issues.

---

## Immutable vs Unmodifiable
- **Immutable** -> state truly cannot change.
- **Unmodifiable view** -> caller may not mutate through this reference, but underlying object may still change elsewhere.

Do not confuse the two.

---

## Common Use Cases
- DTO/value objects
- money/date/range/domain value types
- configuration snapshots
- cache keys
- event payloads

---

## Interview Traps
- class can be final but still mutable
- returning mutable field directly breaks immutability
- arrays are mutable and require defensive copy
- immutability is stronger than read-only access

---

## Interview-Specific Questions
### How do you create an immutable class in Java?
Make the class final, fields private and final, initialize them in constructor, provide no setters, and defensively copy mutable state.

### Why is immutable object thread-safe?
Because its state does not change after construction, so concurrent reads are safe without synchronization.

### Is `String` immutable? Why?
Yes. Its internal state cannot be changed after creation, which supports security, caching, string pool reuse, and thread safety.

### Why defensive copying is needed?
Because mutable fields like arrays, `Date`, or collections can otherwise expose internal state and allow mutation from outside.

### Are records always immutable?
Records make fields final and reduce boilerplate, but if a record field refers to a mutable object, the record is not deeply immutable.

---

## Quick Revision
- Immutable object = no state change after creation.
- `final` helps, but defensive copy is critical.
- Immutable objects are safer, simpler, and concurrency-friendly.
- Returning mutable internals breaks immutability.

