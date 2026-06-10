# Polymorphism

## 1. What is Polymorphism?
**Polymorphism** means **one interface, many forms**.

In Java, the same method call can behave differently based on:
- parameter list (**overloading**)
- actual runtime object type (**overriding**)

### Definition
Polymorphism allows code to work with a common type while behavior varies by implementation.

### Example
```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Dog barking");
    }
}
```

### Production use case
Backend services often depend on abstractions like `PaymentService`, while implementations vary across gateways or environments.

---

## 2. Why Polymorphism is Needed
Polymorphism helps:
- reduce rigid conditionals
- support extensibility
- improve maintainability
- allow interchangeable implementations
- enable loose coupling

### Without polymorphism
Code often grows into long `if-else` or `switch` chains.

### With polymorphism
You call the same contract and let runtime choose the right implementation.

### Backend angle
Common in service interfaces, strategies, repositories, and framework callbacks.

---

## 3. Types of Polymorphism
Java mainly supports:
- **Compile-time polymorphism** -> method overloading
- **Runtime polymorphism** -> method overriding + dynamic dispatch

| Type | Decided When | Example |
|---|---|---|
| Compile-time | compile time | overloaded methods |
| Runtime | runtime | overridden methods |

---

## 4. Compile-time Polymorphism
Compile-time polymorphism happens when the compiler resolves the method call.

### Definition
The compiler chooses the target method based on method signature.

### Example
```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

### Internal working
Compiler matches:
- method name
- parameter count
- parameter types
- parameter order

Return type alone does not overload a method.

### Production use case
Useful in utility APIs, builders, constructors, and convenience methods.

---

## 5. Runtime Polymorphism
Runtime polymorphism happens when a parent reference points to a child object and an overridden method is invoked.

### Example
```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Dog barking");
    }
}

class Demo {
    static void run(Animal a) {
        a.sound();
    }
}
```

### Internal working
Compiler checks only that `sound()` exists in `Animal`. At runtime, JVM uses the actual object type and calls `Dog.sound()` for a `Dog` object.

### Production use case
This is the basis of interface-driven design in Spring Boot and enterprise systems.

---

## 6. Method Overloading
Method overloading means same method name, different parameter list.

### Rules
Overloading can differ by:
- number of parameters
- parameter types
- parameter order

### Example
```java
class Printer {
    void print(String s) {
        System.out.println(s);
    }

    void print(int x) {
        System.out.println(x);
    }
}
```

### Important
Changing only return type does not overload a method.

---

## 7. Method Overriding
Method overriding means a subclass provides its own implementation of an inherited method.

### Example
```java
class PaymentService {
    void pay() {
        System.out.println("Generic payment");
    }
}

class CardPaymentService extends PaymentService {
    @Override
    void pay() {
        System.out.println("Card payment");
    }
}
```

### Production use case
Common in framework extension points, strategies, handlers, and service implementations.

---

## 8. Rules of Method Overriding
- method name must be same
- parameters must be same
- return type must be same or covariant
- access level cannot be more restrictive
- checked exception cannot be broader than parent method

### Cannot be overridden
- `final` methods
- `static` methods (they are hidden)
- `private` methods
- constructors

---

## 9. Covariant Return Type
A child override can return a subtype of parent return type.

### Example
```java
class Animal {}
class Dog extends Animal {}

class Factory {
    Animal create() {
        return new Animal();
    }
}

class DogFactory extends Factory {
    @Override
    Dog create() {
        return new Dog();
    }
}
```

### Why useful
It keeps the parent contract while allowing a more specific return type.

---

## 10. Static Binding
Static binding means method resolution is fixed at compile time.

### Applies to
- method overloading
- `static` methods
- `private` methods
- `final` methods

### Example
```java
class Demo {
    static void show() {
        System.out.println("static show");
    }
}
```

---

## 11. Dynamic Binding
Dynamic binding means JVM selects the actual method implementation at runtime.

### Applies to
Overridden instance methods.

### Example
```java
class Parent {
    void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child");
    }
}
```

If `Parent p = new Child();`, `p.show()` resolves at runtime.

---

## 12. Upcasting
Upcasting means assigning a child object to a parent reference.

### Example
```java
class Animal {}
class Dog extends Animal {}

class Demo {
    void test() {
        Animal a = new Dog();
    }
}
```

### Why used
Upcasting enables runtime polymorphism and programming to abstraction.

---

## 13. Downcasting
Downcasting means converting a parent reference back to child type.

### Example
```java
class Animal {}
class Dog extends Animal {
    void bark() {}
}

class Demo {
    void test() {
        Animal a = new Dog();
        Dog d = (Dog) a;
        d.bark();
    }
}
```

### Risk
If actual object is not of target type, JVM throws `ClassCastException`.

---

## 14. Dynamic Method Dispatch
**Dynamic Method Dispatch** is the runtime process by which JVM chooses an overridden method using actual object type, not reference type.

### Example
```java
class Animal {
    void sound() {
        System.out.println("Animal");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Dog");
    }
}

class Demo {
    static void test() {
        Animal a = new Dog();
        a.sound();
    }
}
```

### Output
```text
Dog
```

### Internal working
- compiler verifies `sound()` against `Animal`
- runtime sees actual object is `Dog`
- JVM dispatches to `Dog.sound()`

### Key line
Reference type decides **what can be called**; object type decides **which overridden method runs**.

---

## 15. Runtime Polymorphism Internals
For code like:
```java
class Demo {
    void test() {
        Parent p = new Child();
        p.show();
    }
}
```

### What happens
1. compiler validates `show()` against `Parent`
2. object created is `Child`
3. runtime uses actual object type
4. `Child.show()` executes

### Important
Fields do not behave polymorphically like methods. Field access depends on reference type.

---

## 16. JVM Method Resolution
JVM resolves methods in stages:
- compile-time symbolic reference is produced
- runtime resolves actual target based on invocation kind
- for overridden instance methods, dynamic lookup occurs

### JVM intuition
Common categories include:
- direct/static calls
- special calls like constructors and `super`
- virtual calls for overridden instance methods

### Interview-safe wording
For overridden instance methods, JVM performs runtime lookup using actual object type.

---

## 17. VTable Internal Working
**VTable (Virtual Method Table)** is a common conceptual model for runtime method dispatch.

### Definition
It is an internal structure used by many runtimes/JVM implementations to map method slots to actual implementations.

### Intuition
- parent class has method entries
- child class overrides some entries
- object points to type metadata
- runtime lookup uses that metadata to find the correct implementation quickly

### Important note
VTable is a helpful interview model; exact JVM implementation details may vary.

---

## 18. Late Binding
Late binding means the final method target is decided at runtime, not compile time.

### Related concept
Runtime polymorphism and dynamic binding are forms of late binding.

### Example
`Animal a = new Dog(); a.sound();`

---

## 19. Why Static Methods Cannot Be Overridden
Static methods belong to the class, not the object.

### Meaning
Overriding needs runtime dispatch based on object type, but static methods are resolved using class/reference context.

### Example
```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {
        System.out.println("Child");
    }
}
```

This is **method hiding**, not overriding.

---

## 20. Real-life Production Examples
- `PaymentService` -> different gateway implementations
- `NotificationService` -> email, SMS, push
- Spring controllers/services often depend on interfaces while actual beans vary by configuration

---

## 21. Tricky Output-based Questions
### 1. Parent reference, child object
```java
class Parent {
    void show() { System.out.println("Parent"); }
}
class Child extends Parent {
    void show() { System.out.println("Child"); }
}
```
```java
class Demo1 {
    void test() {
        Parent p = new Child();
        p.show();
    }
}
```
**Output:** `Child`

### 2. Field access
```java
class Parent {
    String name = "Parent";
}
class Child extends Parent {
    String name = "Child";
}
```
```java
class Demo2 {
    void test() {
        Parent p = new Child();
        System.out.println(p.name);
    }
}
```
**Output:** `Parent`

### 3. Static method
```java
class Parent {
    static void show() { System.out.println("Parent"); }
}
class Child extends Parent {
    static void show() { System.out.println("Child"); }
}
```
```java
class Demo3 {
    void test() {
        Parent p = new Child();
        p.show();
    }
}
```
**Output:** `Parent`
