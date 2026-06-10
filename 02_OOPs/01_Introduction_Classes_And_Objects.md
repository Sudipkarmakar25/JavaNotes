# Introduction, Classes and Objects

## 1. What is OOPs?
**OOPs (Object-Oriented Programming System)** organizes code around **objects** that combine:
- **state** -> fields/data
- **behavior** -> methods

### Why OOPs was introduced
It addresses common procedural-programming problems:
- scattered data and logic
- weak modularity
- difficult maintenance
- poor real-world modeling
- low reuse and extensibility

### Backend relevance
In Java backend and Spring Boot, OOP helps organize code into entities, DTOs, services, repositories, and controllers.

---

## 2. Procedural Programming vs OOPs

| Aspect | Procedural | OOPs |
|---|---|---|
| Focus | functions | objects + responsibilities |
| Data and behavior | separate | bundled |
| Reuse | function reuse | inheritance/composition |
| Maintainability | harder at scale | easier when designed well |
| Real-world modeling | weaker | stronger |

**Key point:** OOP improves structure for large systems, but poor OOP design is still bad design.

---

## 3. Features of OOPs
The four pillars are:
- **Encapsulation**
- **Inheritance**
- **Polymorphism**
- **Abstraction**

This file focuses on the foundation behind those pillars: **class, object, memory, constructors, `this`, `super`**.

---

## 4. What is a Class?
A **class** is a blueprint that defines the data and behavior an object will have.

### Syntax
```java
class User {
    String name;
    int age;

    void login() {
        System.out.println(name + " logged in");
    }
}
```

### Internal view
A class is loaded by the JVM as metadata containing:
- fields
- methods
- constructors
- runtime type information

---

## 5. What is an Object?
An **object** is a runtime instance of a class.

### Syntax
```java
User u = new User();
```

### Internal working
Object creation involves:
1. heap allocation
2. default field initialization
3. constructor execution
4. returning a reference

---

## 6. Class vs Object

| Aspect | Class | Object |
|---|---|---|
| Nature | blueprint | runtime instance |
| Memory | class metadata | heap memory |
| Count | one definition usually | many instances |
| Example | `class User {}` | `new User()` |

A class defines structure; an object is the actual runtime entity.

---

## 7. Memory Creation of Object in Java
### Example
```java
User u = new User();
```

### What happens internally
1. JVM ensures `User` is loaded
2. object memory is allocated on heap
3. instance fields get default values
4. constructor runs
5. reference is assigned to `u`

### Important
`u` is not the object itself; it points to the heap object.

---

## 8. Stack vs Heap Basics

| Area | Stores |
|---|---|
| Stack | method frames, local variables, local references |
| Heap | objects, arrays, instance data |

### Example
```java
void test() {
    User u = new User();
}
```
- `u` -> local reference in stack frame
- `User` object -> heap

GC mainly manages **heap objects**, not local stack variables.

---

## 9. Object Reference vs Actual Object
A reference stores a handle-like value used to access an object. The object stores the real state.

### Example
```java
User a = new User();
User b = a;
```
Both references point to the same object.

```java
class Demo {
    static void test() {
        User a = new User();
        User b = a;
        a.name = "Java";
        System.out.println(b.name); // Java
    }
}
```

### Internal point
Java references are managed by the JVM; they are not exposed as raw pointers.

---

## 10. Constructors
A **constructor** initializes an object during creation.

### Rules
- same name as class
- no return type
- can be overloaded
- runs automatically during `new`

### Example
```java
class User {
    String name;
    int age;

    User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### Types
- default constructor (compiler-generated if needed)
- no-arg constructor
- parameterized constructor

### Production angle
Constructors are central to dependency injection in Spring Boot.

---

## 11. Constructor Chaining
Constructor chaining means one constructor calls another.

### `this()`
```java
class User {
    String name;
    int age;

    User() {
        this("Guest", 0);
    }

    User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### `super()`
Used to call parent constructor.

### Rule
`this()` or `super()` must be the first statement in a constructor.

---

## 12. `this` Keyword
`this` refers to the current object.

### Common uses
- distinguish field and parameter
- call another constructor via `this()`
- pass or return current object

### Example
```java
class User {
    String name;

    User(String name) {
        this.name = name;
    }
}
```

---

## 13. `super` Keyword Basics
`super` refers to the parent part of the current object.

### Uses
- call parent constructor
- call parent method
- access hidden parent field

### Example
```java
class Animal {
    Animal() {
        System.out.println("Animal constructor");
    }
}

class Dog extends Animal {
    Dog() {
        super();
        System.out.println("Dog constructor");
    }
}
```

If omitted, compiler usually inserts `super()` automatically.

---

## 14. Access Modifiers Overview

| Modifier | Same Class | Same Package | Subclass | Other Package |
|---|---|---|---|---|
| `private` | Yes | No | No | No |
| default | Yes | Yes | No* | No |
| `protected` | Yes | Yes | Yes | Yes* |
| `public` | Yes | Yes | Yes | Yes |

Access modifiers protect encapsulation and reduce misuse.

---

## 15. `Object` Class Overview
`java.lang.Object` is the root of almost all Java classes.

### Common methods
- `toString()`
- `equals()`
- `hashCode()`
- `getClass()`
- `wait()`, `notify()`, `notifyAll()`

Every Java object inherits these basics.

---

## 16. Object Creation Internal Working
### Simplified flow
1. class loading
2. heap allocation
3. object header creation
4. default field initialization
5. constructor-chain execution
6. reference return

### Advanced points
- **Object header** stores JVM metadata such as class reference, lock state, and hash-related info.
- **Heap allocation** is optimized heavily and is often very fast.
- **Reference variable** stores only a JVM-managed reference, not full object data.

---

## 17. JavaBean Basics
A **JavaBean** usually has:
- private fields
- public getters/setters
- public no-arg constructor

### Example
```java
public class UserBean {
    private String name;

    public UserBean() {}

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

### Usage
Common in frameworks, binding, serialization, and older Java conventions.

---

## 18. Real-life Examples of Classes and Objects

| Class | Objects |
|---|---|
| `Car` | BMW, Audi, Tesla |
| `User` | customer1, admin1 |
| `Order` | order#1001, order#1002 |
| `Product` | Laptop, Phone, Keyboard |

---

## 19. Common Mistakes Beginners Make
- treating reference and object as same thing
- confusing class definition with runtime object
- ignoring stack vs heap basics
- assuming constructor is the only step in object creation
- using public fields everywhere
- forgetting `this()` / `super()` ordering rules

---

## 20. Best Practices
- keep fields `private`
- initialize objects into valid state
- prefer constructor injection in Spring Boot
- keep classes focused on one responsibility
- use meaningful names
- prefer immutability for DTO/value objects where practical
- override `equals()`, `hashCode()`, and `toString()` when appropriate
