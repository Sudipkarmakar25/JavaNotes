# Inner Classes - Advanced Interview Notes

## Why a Second Note?
This file focuses on **advanced inner-class behavior** usually asked in interviews:
- anonymous classes
- variable capture
- synthetic outer references
- memory/performance implications
- inner classes vs lambdas

---

## Anonymous Inner Class vs Lambda
### Anonymous Inner Class
```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("run");
    }
};
```

### Lambda
```java
Runnable r = () -> System.out.println("run");
```

### Important difference
A lambda is **not** just syntax sugar for an anonymous class in the strict runtime sense.
They differ in generated behavior, `this` binding, and implementation details.

---

## `this` Keyword Difference
### In anonymous inner class
`this` refers to the anonymous class instance.

### In lambda
`this` refers to the enclosing outer instance.

This is a common advanced interview question.

---

## Variable Capture
Local variables used inside local/anonymous classes are captured by value-like semantics and must be final or effectively final.

```java
int x = 10;
Runnable r = () -> System.out.println(x);
```

You cannot later do:
```java
x = 20; // not allowed if captured
```

---

## Why Effectively Final?
Because local variables live on the stack, while inner class/lambda behavior may outlive that method execution.
The compiler captures a stable value snapshot, not a mutable stack slot.

---

## Synthetic Outer Reference
A non-static inner class generally holds an implicit reference to outer instance.

Why it matters:
- can access outer private members
- increases coupling
- may contribute to accidental memory retention if inner object outlives intended outer usage

---

## Memory Leak Style Concern
If a long-lived inner class instance implicitly keeps outer object alive, the outer object may not be GC-eligible.

This is one reason static nested classes are often preferred when outer instance is not needed.

---

## Inner Class Access to Private Members
Compiler may generate synthetic bridge/access methods so nested classes can access private members across the boundary.

You usually do not write this yourself, but interviewers may ask how it works internally.

---

## When to Prefer Static Nested Class
Prefer static nested class when:
- no outer instance data is needed
- you want lower coupling
- you want to avoid implicit outer reference
- the nested type is only for namespacing/organization

---

## Anonymous Class Use Cases
- one-off strategy implementation
- comparator/callback/event handler
- quick interface or abstract-class instantiation

But in modern Java, lambdas often replace anonymous classes for functional interfaces.

---

## Interview-Specific Questions
### Difference between anonymous inner class and lambda?
Anonymous inner class creates a class instance with its own `this`; lambda is a functional-style construct with different runtime semantics and outer `this` binding.

### Why are captured local variables effectively final?
Because the compiler captures their value, and mutable local stack variables cannot be safely shared after method scope changes.

### Can inner class have static members?
A normal inner class cannot declare most static members except compile-time constants. A static nested class can have static members.

### Why prefer static nested class sometimes?
Because it avoids implicit outer reference and reduces memory/coupling overhead.

### Can inner class cause memory issues?
Yes, if it unintentionally keeps the outer instance reachable for longer than needed.

---

## Quick Revision
- anonymous class and lambda are related but not identical.
- captured locals must be effectively final.
- non-static inner class keeps outer reference.
- use static nested class when outer instance is unnecessary.

