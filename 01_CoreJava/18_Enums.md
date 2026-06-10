# Enums in Java

## What Is an Enum?
An enum is a special type representing a fixed set of constants.

```java
enum Status {
    NEW, IN_PROGRESS, DONE
}
```

Use enums when values are finite, known, and meaningful as a domain concept.

---

## Why Enums Are Better Than `int` Constants
- type-safe
- readable
- namespace-scoped
- can have fields, methods, constructors
- work well with `switch`, `EnumSet`, `EnumMap`

---

## Enum Is More Than Just Constants
Each enum constant is an object instance.
All enums implicitly extend `java.lang.Enum`.

```java
enum Role {
    ADMIN, USER
}
```

---

## Enum with Fields and Constructor
```java
enum Status {
    NEW("N"), DONE("D");

    private final String code;

    Status(String code) {
        this.code = code;
    }

    public String getCode() {
        return code;
    }
}
```

### Important
Enum constructors are implicitly private.
You cannot instantiate enum using `new`.

---

## Enum with Methods
Enums can contain business behavior.

```java
enum Operation {
    ADD {
        public int apply(int a, int b) { return a + b; }
    },
    SUBTRACT {
        public int apply(int a, int b) { return a - b; }
    };

    public abstract int apply(int a, int b);
}
```

This is called constant-specific behavior.

---

## Useful Built-in Enum Methods
- `values()`
- `valueOf(String name)`
- `name()`
- `ordinal()`

### Important caution
Avoid using `ordinal()` in business logic or persistence. It is fragile if enum order changes.

---

## `EnumSet` and `EnumMap`
### `EnumSet`
High-performance set implementation for enum values.

### `EnumMap`
Efficient map implementation with enum keys.

They are usually better than generic `HashSet`/`HashMap` for enum keys/values.

---

## Enums in `switch`
```java
switch (status) {
    case NEW -> System.out.println("new");
    case DONE -> System.out.println("done");
}
```

Enums are a natural fit for branching on fixed domain states.

---

## Enum Singleton Pattern
Single-element enum is the safest singleton implementation in Java.

```java
enum AppConfig {
    INSTANCE;
}
```

Why interviewers like this:
- serialization-safe
- reflection-resistant compared to many manual singleton styles
- simple and robust

---

## Interview Traps
- enum is not just a list of constants; it is a class-like type
- enum constructor cannot be public/protected
- `ordinal()` should not drive business logic
- enum can implement interfaces
- enum cannot extend another class because it already extends `Enum`

---

## Interview-Specific Questions
### What is an enum in Java?
A special type used to represent a fixed set of named constants, where each constant is actually an object instance.

### Why use enum instead of constants?
Because enum is type-safe, readable, extensible with methods/fields, and less error-prone than raw constants.

### Can enum have constructor and methods?
Yes. Enum can have fields, constructors, methods, and even constant-specific behavior.

### Can enum implement interfaces?
Yes.

### Why is enum singleton considered strong?
Because it is naturally protected against serialization and many reflection-related issues.

---

## Quick Revision
- enum = fixed set of type-safe constants.
- enum constants are objects.
- enums can have fields, methods, constructors.
- prefer `EnumSet` and `EnumMap` for enum-heavy collections.
- avoid business dependency on `ordinal()`.

