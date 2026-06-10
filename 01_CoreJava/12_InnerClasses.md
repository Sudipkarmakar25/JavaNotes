# Inner Classes - Foundations

## What Are Inner / Nested Classes?
A class defined inside another class is called a nested class. In common interview usage, people often call all of them "inner classes", but technically there is a difference.

### Categories
- **Static nested class**
- **Member inner class**
- **Local inner class**
- **Anonymous inner class**

Strictly speaking, non-static nested classes are the actual inner classes.

---

## Why Use Nested Classes?
- logically group closely related types
- improve encapsulation
- hide implementation details
- access outer class members naturally
- useful in callbacks, builders, DSL-style APIs

---

## 1. Static Nested Class
```java
class Outer {
    static class Nested {
        void show() {
            System.out.println("static nested");
        }
    }
}
```

### Key points
- behaves almost like a normal class scoped inside outer class
- can access only outer static members directly
- does not need outer object instance

---

## 2. Member Inner Class
```java
class Outer {
    private int x = 10;

    class Inner {
        void show() {
            System.out.println(x);
        }
    }
}
```

### Key points
- tied to outer object instance
- can access all outer members, including private ones
- created using outer object

```java
Outer o = new Outer();
Outer.Inner i = o.new Inner();
```

---

## 3. Local Inner Class
Declared inside a method/block.

```java
void test() {
    class Local {
        void print() {}
    }
}
```

### Key points
- scope limited to method/block
- can access local variables only if final or effectively final

---

## 4. Anonymous Inner Class
A class without explicit name, declared and instantiated in one step.

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("run");
    }
};
```

Common in old callback/event code.

---

## Access Rules
Inner classes can access outer private members because compiler generates synthetic support mechanisms.

Local/anonymous classes can capture only final/effectively final local variables because local variables live on stack, while captured state may outlive method execution.

---

## When to Use What
- static nested class -> helper type not needing outer instance
- member inner class -> behavior tightly coupled to outer object
- local inner class -> small method-local helper
- anonymous inner class -> one-off implementation

---

## Interview-Specific Questions
### Difference between nested class and inner class?
Nested class is the broader term. Static nested class is not an inner class technically; non-static nested classes are inner classes.

### Can inner class access private members of outer class?
Yes.

### Why local variables used inside inner class must be final/effectively final?
Because the inner class captures their value, and mutable local stack variables would create lifetime/consistency issues.

### How do you create an object of member inner class?
Using outer object first: `Outer.Inner i = outer.new Inner();`

### Can static nested class access outer instance members directly?
No. It can access only outer static members directly.

---

## Quick Revision
- nested class types: static nested, member, local, anonymous.
- member inner class needs outer object.
- static nested class does not.
- local/anonymous capture effectively final variables.

