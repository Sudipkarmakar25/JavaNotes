# Static Keyword in Java

## What `static` Means
`static` makes a member belong to the **class**, not to individual objects.

There is one shared copy per class loader.

---

## Where `static` Can Be Used
- static variable
- static method
- static block
- static nested class
- static import (language feature usage)

You cannot make a top-level class `static`.

---

## Static Variables
Shared across all instances.

```java
class Counter {
    static int count = 0;
    Counter() { count++; }
}
```

### Key point
If 100 objects are created, `count` is still one shared variable.

---

## Static Methods
Can be called using class name.

```java
class MathUtil {
    static int add(int a, int b) {
        return a + b;
    }
}
```

### Rules
- static method can directly access only static members
- it cannot use `this` or `super`
- it can access instance members only through an object reference

---

## Static Block
Used for class-level initialization.
Executed once when the class is initialized.

```java
class Demo {
    static {
        System.out.println("static block executed");
    }
}
```

Useful for complex static initialization logic.

### Internal JVM view
- static field initializers and static blocks are combined by the compiler into a special class initialization method called `<clinit>`
- the JVM runs `<clinit>` once per class loader
- if multiple threads try to initialize the same class, JVM ensures class initialization is synchronized so it happens safely once

---

## Static Nested Class
A nested class declared `static`.

```java
class Outer {
    static class Inner {
        void show() {
            System.out.println("static nested class");
        }
    }
}
```

### Important
A static nested class does **not** need an outer class object.
Unlike non-static inner classes, it does not hold implicit outer-instance reference.

---

## Static and Memory
- static members are associated with class metadata and logically shared state
- they are not part of each object instance
- they live as long as their class remains loaded

### Internal handling
- every object instance stores only instance fields; static fields are not copied into each object
- static state is tied to the `Class` metadata/runtime representation of that type
- there is one copy per **loaded class**, which practically means one copy per **class loader**
- if the same class is loaded by two different class loaders, they get separate static state

Example interview idea:
> Same class name loaded by different class loaders can have different static values because JVM treats them as different runtime types.

---

## How JVM Handles `static`
### 1. Loading
The class loader reads the `.class` bytecode and creates internal runtime metadata for the class.

At this point, the JVM knows the class structure:
- class name
- parent class
- interfaces
- fields
- methods
- constant pool entries

### 2. Linking
Linking has three major parts:

#### a. Verification
JVM checks the bytecode format, type safety, and structural correctness.

#### b. Preparation
Memory is allocated for static fields and they get **default values**.

Example:
```java
static int count = 10;
```
During preparation, `count` first becomes `0`, not `10`.

#### c. Resolution
Symbolic references from the constant pool are converted into direct runtime references.

For example, a symbolic reference to `MathUtil.add` or `Counter.count` gets resolved to the actual loaded runtime member.

### 3. Initialization
Only now does the JVM execute class initialization code:
- explicit static field assignments
- static blocks
- compiler-generated `<clinit>` logic

So in the previous example:
```java
static int count = 10;
```
the value becomes `10` during **initialization**, not preparation.

---

## When Static Initialization Happens
A class is usually initialized on its first **active use**, such as:
- creating an object with `new`
- accessing a non-compile-time static field
- calling a static method
- loading certain reflective usages

### Important trap
Compile-time constants may be inlined by the compiler.

```java
class Demo {
    static final int X = 10;
}
```

Using `Demo.X` may not trigger full class initialization if the compiler inlined the constant.

This is a favorite interview trap.

---

## JVM Bytecode for Static Members
The JVM has dedicated bytecode instructions for static access.

### Common instructions
- `getstatic` -> read static field
- `putstatic` -> write static field
- `invokestatic` -> call static method

### Meaning
The JVM does not need an object reference for these operations.
That is why static members are class-level and can be used without creating an instance.

Example mental mapping:
```java
Counter.count++;
MathUtil.add(1, 2);
```

Rough JVM view:
- resolve symbolic reference to `Counter.count`
- execute `getstatic`
- modify value
- execute `putstatic`
- resolve symbolic reference to `MathUtil.add`
- execute `invokestatic`

---

## How JVM Interprets Static Calls
For an instance method, runtime dispatch may depend on actual object type.
For a static method, dispatch is resolved using the **class reference**, not polymorphic object dispatch.

That is why:
- static methods are not overridden
- static methods are bound more like class-level calls
- method hiding happens based on reference/class name used at compile time

This is also why static access is usually faster to reason about conceptually: it does not depend on object identity.

---

## Static and Class Loaders
Static data belongs to the runtime class loaded by a specific class loader.

### Why this matters
- app servers may load same class in isolated loaders
- plugins/modules may hold separate copies of static state
- class-loader leaks can keep static data alive longer than expected

### Interview-friendly line
`static` is shared globally only within the scope of a particular loaded class, not necessarily across the entire JVM.

---

## Class Loaders and Static Access
When JVM needs a class, class loaders participate in locating and defining it.

Common built-in loaders:
- **Bootstrap ClassLoader** -> core Java classes like `java.lang.*`
- **Platform / Extension ClassLoader** -> platform libraries (`Extension` term is older JDK wording)
- **Application ClassLoader** -> application classes from classpath/module path

Once the class is loaded and initialized, its static members become available through that runtime class definition.

---

## Parent Delegation and Why It Matters for `static`
Class loaders usually follow the **parent delegation model**:
1. ask parent first
2. parent tries to load class
3. child loads only if parent cannot

### Why important
- prevents multiple core-class copies in normal flow
- improves security
- ensures consistent class identity

If class identity changes because class loaders differ, static state also becomes separate.

---

## Method Hiding vs Overriding
Static methods are **hidden**, not overridden.

```java
class Parent {
    static void show() {}
}
class Child extends Parent {
    static void show() {}
}
```

Method selection depends on **reference type**, not runtime polymorphism.

---

## Common Uses of `static`
- constants with `static final`
- utility classes and helper methods
- counters/shared caches
- factory methods
- entry point: `public static void main`

---

## Pitfalls
- too much static state hurts testability
- shared mutable static fields can cause race conditions
- static initialization order can create hard-to-debug issues
- static methods do not participate in runtime polymorphism
- class-loader boundaries can create separate copies of static state
- compile-time constants may behave differently from normal static fields during initialization

---

## Interview-Specific Questions
### Why is `main` method static?
Because JVM must call it without creating an object of the class.

### Can a static method access non-static members directly?
No, because non-static members belong to object instances.

### Where are static variables stored?
They are associated with class-level data in JVM-managed shared memory, conceptually in class metadata/shared area rather than per-object memory.

### What is the difference between preparation and initialization for static fields?
During preparation, static fields get default values like `0` or `null`. During initialization, explicit assignments and static blocks run.

### How does JVM access static fields and methods internally?
Using constant-pool resolution plus bytecode instructions like `getstatic`, `putstatic`, and `invokestatic`.

### Are static variables shared across the whole JVM?
Not always. They are shared per loaded class, which effectively means per class loader.

### Why can `static final` constants behave differently during class loading?
Because compile-time constants may be inlined by the compiler, so reading them may not trigger normal class initialization.

### Can we override static methods?
No. They are hidden, not overridden.

### Difference between static nested class and inner class?
Static nested class does not need outer object reference; inner class does.

---

## Quick Revision
- `static` means class-level.
- static members are shared.
- shared usually means one copy per class loader.
- static methods cannot use `this`.
- static block runs once during class initialization.
- static initialization logic is compiled into `<clinit>`.
- preparation gives default values; initialization applies explicit values.
- JVM uses `getstatic`, `putstatic`, and `invokestatic` for static access.
- static methods are hidden, not overridden.

