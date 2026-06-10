# Shallow Copy vs Deep Copy

## Core Difference
### Shallow Copy
Creates a new object, but nested referenced objects are still shared.

### Deep Copy
Creates a new object and recursively copies nested mutable objects too.

---

## Example
```java
class Address {
    String city;
}

class Employee {
    String name;
    Address address;
}
```

If only `Employee` object is copied but both employees point to same `Address`, that is shallow copy.

---

## Shallow Copy Behavior
```java
Employee e2 = e1;
```
This is not even a copy; it is just reference assignment.

A real shallow copy creates another outer object but shares inner references.

```java
Employee e2 = new Employee();
e2.name = e1.name;
e2.address = e1.address;
```

Change in `address.city` affects both objects.

---

## Deep Copy Behavior
```java
Employee e2 = new Employee();
e2.name = e1.name;
e2.address = new Address();
e2.address.city = e1.address.city;
```

Now nested mutable state is separated.

---

## `clone()` and Copying
Java provides `clone()`, but it is widely considered awkward because:
- `Cloneable` is marker-only and confusing
- `Object.clone()` performs shallow field copy by default
- deep copy still requires manual handling
- it interacts poorly with inheritance

In real projects, copy constructors/factory methods are often preferred.

---

## Common Deep Copy Strategies
- copy constructor
- factory method
- manual recursive copying
- serialization-based copy (slow and specialized)
- library-based mapping/copying

---

## Immutable Objects Reduce Copy Complexity
If nested objects are immutable, shallow copy may already be safe enough because shared state cannot be mutated.

---

## Interview Traps
- assignment is not copying
- `clone()` default is shallow
- strings are safe to share because immutable
- deep copy matters only for mutable nested state

---

## Interview-Specific Questions
### Difference between shallow copy and deep copy?
Shallow copy duplicates the outer object but shares nested references, while deep copy duplicates both the outer object and nested mutable objects.

### Does `clone()` create shallow or deep copy?
By default, `Object.clone()` creates a shallow copy.

### Why is `clone()` discouraged sometimes?
Because it is error-prone, inheritance-unfriendly, and still requires custom logic for deep copying.

### Is reference assignment a copy?
No. It only makes another reference point to the same object.

### When is shallow copy acceptable?
When nested objects are immutable or sharing them is intentionally safe.

---

## Quick Revision
- assignment != copy.
- shallow copy shares nested references.
- deep copy duplicates mutable object graph.
- `clone()` is shallow by default.
- immutable fields make copying safer.

