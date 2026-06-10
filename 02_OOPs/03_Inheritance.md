# Inheritance

## 1. What is Inheritance?
**Inheritance** is the OOP mechanism where one class acquires properties and behavior of another class.

- **Parent / Superclass** -> existing class
- **Child / Subclass** -> derived class

### Definition
Inheritance models an **IS-A** relationship.

### Syntax
```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking");
    }
}
```

### Internal working
A child class does not physically copy a parent object. JVM treats the subclass as a type that includes inherited accessible members and participates in lookup through the inheritance chain.

### Production use case
Useful when a child is genuinely a specialized form of parent and the hierarchy is stable.

---

## 2. Why Inheritance is Needed
Inheritance helps:
- reuse common code
- express hierarchy clearly
- support polymorphism
- centralize shared behavior
- reduce duplication in stable hierarchies

### Example
```java
class Employee {
    String name;
    void work() {}
}

class Developer extends Employee {
    void writeCode() {}
}
```

### Production angle
Helpful in some framework abstractions, base entities, and reusable infrastructure code, but overuse increases coupling.

---

## 3. Types of Inheritance in Java
### Supported through classes
- **Single inheritance**
- **Multilevel inheritance**
- **Hierarchical inheritance**

### Not supported through classes
- **Multiple inheritance of classes**
- **Hybrid inheritance via classes**

### Examples
```text
Single:       A -> B
Multilevel:   A -> B -> C
Hierarchical: A -> B, A -> C
```

### Note
Java allows multiple inheritance of **contracts** through interfaces, not through concrete classes.

---

## 4. `extends` Keyword
`extends` is used to inherit from a class.

### Syntax
```java
class Dog extends Animal {
}
```

### Meaning
`Dog` gets accessible fields and methods of `Animal` and may add or override behavior.

### Internal working
Inheritance affects type checking at compile time, while runtime lookup follows the child-to-parent chain.

---

## 5. `super` Keyword
`super` refers to the immediate parent part of the current object.

### Uses
- call parent constructor
- call parent method
- access hidden parent field

### Example
```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    void sound() {
        super.sound();
        System.out.println("Dog barking");
    }
}
```

### Production use case
Useful when extending framework or base behavior while still reusing parent logic.

---

## 6. Constructor Calling Order
When a child object is created, parent constructor runs first.

### Example
```java
class Animal {
    Animal() {
        System.out.println("Animal constructor");
    }
}

class Dog extends Animal {
    Dog() {
        System.out.println("Dog constructor");
    }
}
```

### Output
```text
Animal constructor
Dog constructor
```

### Rule
If not written explicitly, compiler inserts `super()` when possible.

---

## 7. Method Inheritance
Child classes inherit accessible parent methods.

### Example
```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {}
```

```java
class Demo {
    void test() {
        Dog d = new Dog();
        d.eat();
    }
}
```

### Internal working
Method lookup starts from the runtime type and moves upward when needed.

### Important
- constructors are not inherited
- private methods are not inherited as accessible methods

---

## 8. IS-A Relationship
Inheritance should represent **IS-A**.

### Examples
- `Dog IS-A Animal`
- `SavingsAccount IS-A Account`

### Wrong example
- `Engine IS-A Car` -> wrong

If IS-A is not natural, inheritance is usually poor design.

---

## 9. HAS-A Relationship
**HAS-A** represents containment rather than inheritance.

### Examples
- `Car HAS-A Engine`
- `Order HAS-A PaymentDetails`

### Syntax
```java
class Engine {}

class Car {
    private Engine engine = new Engine();
}
```

### Production angle
Enterprise systems usually prefer HAS-A via composition over deep inheritance.

---

## 10. Association
**Association** is a general relationship where one object uses or knows another.

### Example
```java
class Doctor {}
class Patient {}
```
A doctor treats patients, but both can exist independently.

### Production use case
Service-to-repository or service-to-client relationships are common associations.

---

## 11. Aggregation
**Aggregation** is a weak HAS-A relationship where the contained object can exist independently.

### Example
```java
class Department {}

class University {
    private Department department;
}
```

### Production use case
A `Team` may aggregate `Employee` objects that also exist elsewhere in the system.

---

## 12. Composition
**Composition** is a strong HAS-A relationship where lifecycle depends on the owner.

### Example
```java
class Engine {}

class Car {
    private final Engine engine = new Engine();
}
```

### Internal meaning
The outer object strongly owns the inner part.

### Production use case
Useful for helpers, policies, and domain parts that should not exist independently in that context.

---

## 13. Composition vs Inheritance

| Aspect | Inheritance | Composition |
|---|---|---|
| Relationship | IS-A | HAS-A |
| Coupling | higher | lower |
| Flexibility | lower | higher |
| Reuse style | hierarchy-based | collaboration-based |
| Change impact | broader | more localized |

Inheritance reuses by hierarchy; composition reuses by delegation.

---

## 14. Why Composition is Preferred over Inheritance
Composition is often preferred because it:
- reduces tight coupling
- avoids fragile hierarchies
- allows behavior to change independently
- works well with dependency injection
- is easier to test and maintain

### Example idea
Instead of:
```text
ReportService extends FileExporter
```
prefer:
```text
ReportService has Exporter
```

### Production use case
Spring applications strongly favor composition + interfaces + DI.

---

## 15. Tight Coupling Problems in Inheritance
Inheritance tightly couples child to parent implementation.

### Problems
- child depends on parent changes
- base-class changes may break many subclasses
- deep hierarchies become hard to understand
- subclasses may inherit methods they should not expose
- overriding may create inconsistent behavior

### Key point
Inheritance is fragile when used only for code reuse instead of true modeling.

---

## 16. Real-life Examples
### Good inheritance
- `SavingsAccount extends Account`
- `Circle extends Shape`
- `RuntimeException extends Exception`

### Good composition
- `Car has Engine`
- `Order has Address`
- `UserService has UserRepository`

### Backend example
In Spring Boot, service classes usually prefer composition and injected dependencies over inheritance-heavy design.

---

## 17. Diamond Problem
The diamond problem occurs when a class inherits the same method implementation through multiple parent classes, causing ambiguity.

### Diagram
```text
    A
   / \
  B   C
   \ /
    D
```

### Why Java avoids it for classes
Java does not allow multiple inheritance of classes.

### Interface note
With interfaces, conflicts must be resolved explicitly.

---

## 18. Multiple Inheritance in Java
### Classes
Java does not support multiple inheritance of classes.

```java
class A {}
class B {}
// class C extends A, B {} // invalid
```

### Interfaces
Java allows one class to implement multiple interfaces.

```java
interface A { void m(); }
interface B { void n(); }
class C implements A, B {
    public void m() {}
    public void n() {}
}
```

### Why
This preserves flexibility without class-level ambiguity.

---

## 19. Constructor Resolution Internals
When you create a child object:
1. memory for the full child object is allocated
2. parent constructor chain starts first
3. parent state initializes
4. child state initializes
5. child constructor body executes

### Example flow
```text
Object -> Parent -> Child
```

### Important rule
If parent has no accessible no-arg constructor, child must explicitly call a valid parent constructor.

### Trap
Constructors are not inherited, but constructor chaining always happens during subclass creation.

---

## 20. Production Design Tradeoffs
### Use inheritance when
- the relationship is truly IS-A
- hierarchy is stable
- parent defines meaningful reusable behavior
- polymorphism genuinely helps the design

### Prefer composition when
- you only want reuse
- behavior varies independently
- you want loose coupling
- dependencies should be replaceable or mockable

### Spring Boot angle
Framework code may use inheritance in some base abstractions, but application code usually benefits more from composition.

---

## 21. Common Mistakes
- using inheritance just for reuse
- forcing IS-A where HAS-A is correct
- creating deep inheritance chains
- forgetting parent constructor requirements
- exposing too much inherited behavior
- confusing association, aggregation, and composition

---

## 22. Best Practices
- use inheritance only for true IS-A relationships
- prefer composition for flexibility
- keep hierarchies shallow
- design parent classes carefully
- avoid inheriting from unstable classes
- favor interfaces + composition in Spring Boot code
- make constructor requirements explicit

