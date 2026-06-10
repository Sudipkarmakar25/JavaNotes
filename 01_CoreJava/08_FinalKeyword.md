# Final Keyword in Java

## Meaning of `final`
`final` is a restriction keyword. Its exact meaning depends on where it is used.

It can be applied to:
- variables
- methods
- classes
- parameters
- local variables

---

## Final Variable
A `final` variable can be assigned only once.

```java
final int x = 10;
// x = 20; // compile-time error
```

### Important nuance
If a reference variable is final, the **reference cannot change**, but the object's internal state may still change.

```java
final List<String> list = new ArrayList<>();
list.add("A"); // allowed
// list = new ArrayList<>(); // not allowed
```

---

## Blank Final Variable
A final variable can be assigned later, but only once.

```java
class User {
    final int id;
    User(int id) {
        this.id = id;
    }
}
```

Useful when value is known only at construction time.

---

## Final Method
A final method cannot be overridden in subclass.

```java
class Parent {
    final void show() {}
}
```

This is used to lock behavior.

---

## Final Class
A final class cannot be inherited.

```java
final class Utility {}
```

Examples in JDK: `String`, wrapper classes.

### Why make a class final?
- preserve invariants
- security
- immutability-oriented design
- avoid risky subclassing

---

## Final Parameters and Local Variables
A final parameter/local variable cannot be reassigned.

```java
void print(final int x) {
    // x = 5; // not allowed
}
```

### Effectively final
Even without explicit `final`, a variable is **effectively final** if not reassigned.
This matters for lambdas and inner classes.

---

## Final and Immutability
`final` alone does **not** make an object immutable.
Immutability requires:
- no state mutation after construction
- no setters / mutable escape
- defensive copying where needed

`final` helps but is not sufficient.

---

## Final and Java Memory Model
Final fields have stronger visibility guarantees after constructor completion.
If the object is safely published, other threads are more likely to see correctly initialized final fields.

This is an advanced interview point.

---

## Common Uses
- constants: `public static final`
- value-object fields
- APIs that must not be overridden
- preventing inheritance of sensitive/core classes

---

## Interview Traps
- final variable is not necessarily immutable
- final method can still be overloaded
- final class means no inheritance, not no object creation
- final reference != immutable object

---

## Interview-Specific Questions
### What is the difference between final, finally, and finalize?
`final` is a keyword, `finally` is a block in exception handling, and `finalize` was an object cleanup method now deprecated and unreliable.

### Can we make constructor final?
No. Constructors are never inherited, so `final` is not allowed.

### Can a final class have final methods?
Yes, but it is redundant because the class itself cannot be extended.

### Does final improve thread safety?
It can help by preventing reassignment and giving better initialization guarantees for final fields, but it does not automatically make code thread-safe.

### Why is `String` final?
To preserve immutability, security, and consistent behavior.

---

## Quick Revision
- final variable -> assign once.
- final method -> cannot override.
- final class -> cannot extend.
- final reference does not mean immutable object.
- final fields have important visibility semantics.

