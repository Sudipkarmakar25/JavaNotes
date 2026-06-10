# Java 8: Functional Interfaces and Lambda Expressions

## 1. Why Java 8 Was Introduced

### Short Definition
Java 8 was introduced to make Java code more expressive, concise, and better suited for modern multi-core and data-processing applications.

### Important Points
- Reduced boilerplate for callbacks and small behavior implementations.
- Introduced **lambda expressions** and **functional interfaces**.
- Enabled easier collection processing through **Streams**.
- Improved API design using `Optional`, Date-Time API, and method references.
- Helped Java write code in a more functional style without removing OOP.

### Simple Example
Before Java 8:
```java
Collections.sort(names, new Comparator<String>() {
	@Override
	public int compare(String a, String b) {
		return a.compareTo(b);
	}
});
```

Java 8:
```java
Collections.sort(names, (a, b) -> a.compareTo(b));
```

### Interview Notes
- Java 8 was not only about lambdas; it modernized the language and libraries.
- Main goal: **less boilerplate + better collection/data processing + cleaner APIs**.

---

## 2. Functional Interface

### Short Definition
A functional interface is an interface with exactly **one abstract method**.

### Important Points
- It may contain multiple **default** and **static** methods.
- It is the target type for lambda expressions.
- Common examples: `Runnable`, `Callable`, `Comparator`.

### Simple Example
```java
interface Calculator {
	int add(int a, int b);
}
```

Using lambda:
```java
Calculator c = (a, b) -> a + b;
System.out.println(c.add(2, 3));
```

### Interview Notes
- One abstract method is the rule, not one total method.
- Methods from `Object` like `toString()` do not break functional-interface status.

---

## 3. `@FunctionalInterface`

### Short Definition
`@FunctionalInterface` is an annotation that tells the compiler the interface must remain functional.

### Important Points
- Optional, but recommended.
- Gives compile-time safety if someone adds a second abstract method.
- Improves readability in team code.

### Simple Example
```java
@FunctionalInterface
interface Greeting {
	void sayHello(String name);
}
```

### Interview Notes
- It is a **validation annotation**, not a feature creator.
- An interface can still be functional without this annotation.

---

## 4. Core Built-in Functional Interfaces

### Short Definition
Java 8 provides common functional interfaces in `java.util.function` to avoid writing custom interfaces repeatedly.

| Interface | Purpose | Method | Example Use |
|---|---|---|---|
| `Predicate<T>` | Tests a condition | `boolean test(T t)` | filter users by age |
| `Function<T, R>` | Converts one type to another | `R apply(T t)` | entity to DTO |
| `Consumer<T>` | Consumes input, returns nothing | `void accept(T t)` | logging, printing |
| `Supplier<T>` | Supplies data, takes no input | `T get()` | lazy object creation |

### Examples

#### `Predicate`
```java
Predicate<Integer> isEven = n -> n % 2 == 0;
System.out.println(isEven.test(10)); // true
```

#### `Function`
```java
Function<String, Integer> lengthFn = s -> s.length();
System.out.println(lengthFn.apply("Java")); // 4
```

#### `Consumer`
```java
Consumer<String> printer = msg -> System.out.println(msg);
printer.accept("Hello");
```

#### `Supplier`
```java
Supplier<Double> randomValue = () -> Math.random();
System.out.println(randomValue.get());
```

### Interview Notes
- `Predicate` is for boolean checks.
- `Function` transforms data.
- `Consumer` performs side effects.
- `Supplier` is often used in lazy loading and object creation.

---

## 5. Lambda Expressions

### Short Definition
A lambda expression is a short way to provide the implementation of a functional interface.

### Syntax
```java
(parameters) -> expression
(parameters) -> { statements; }
```

### Important Points
- Works only with functional interfaces.
- Reduces anonymous-class boilerplate.
- Type is usually inferred by the compiler.
- Can capture local variables only if they are effectively final.

### Simple Example
```java
Runnable task = () -> System.out.println("Running...");
task.run();
```

### Internal Note
- Lambda does not create a named class in source code.
- JVM links lambda behavior using `invokedynamic` and runtime machinery.
- This is lighter and cleaner than many anonymous class use cases.

### Interview Notes
- Lambda is **behavior as data**.
- Frequently used with Streams, callbacks, sorting, filtering, and event handling.

---

## 6. Lambda vs Anonymous Class

| Point | Lambda | Anonymous Class |
|---|---|---|
| Boilerplate | Very low | More verbose |
| `this` keyword | Refers to enclosing object | Refers to anonymous object |
| Target type | Functional interface only | Any interface/abstract class |
| Readability | Better for small behavior | Better for complex multi-method logic |

### Example
Anonymous class:
```java
Runnable r1 = new Runnable() {
	@Override
	public void run() {
		System.out.println("Anonymous");
	}
};
```

Lambda:
```java
Runnable r2 = () -> System.out.println("Lambda");
```

### Interview Notes
- Use lambda for short functional behavior.
- Use anonymous class when you need extra state or more verbose logic.
- Common question: in lambda, `this` belongs to the outer class.

---

## 7. Method References

### Short Definition
A method reference is a shorter form of lambda when the lambda only calls an existing method.

### Important Points
- Improves readability.
- Reuses existing methods directly.
- Common forms:
  - `ClassName::staticMethod`
  - `objectRef::instanceMethod`
  - `ClassName::instanceMethod`
  - `ClassName::new`

### Simple Example
Lambda:
```java
Consumer<String> c1 = s -> System.out.println(s);
```

Method reference:
```java
Consumer<String> c2 = System.out::println;
```

### Interview Notes
- Method reference is not a separate feature; it is a compact form of lambda.
- Use it only when it improves readability.

---

## 8. Effectively Final Variables

### Short Definition
A local variable used inside a lambda must be **final or effectively final**, meaning its value is not changed after initialization.

### Important Points
- Applies to local variables captured by lambdas.
- Prevents unsafe access to mutable local state.
- Instance and static variables do not have this restriction in the same way.

### Simple Example
Valid:
```java
int base = 10;
Function<Integer, Integer> addBase = n -> n + base;
System.out.println(addBase.apply(5));
```

Invalid:
```java
int base = 10;
base++;
Function<Integer, Integer> addBase = n -> n + base; // compile-time error
```

### Interview Notes
- Local variables live on the stack, but lambdas may outlive the method scope.
- Java captures the value safely, so mutation is restricted.

---

## 9. Quick Interview Recap

### Common Questions

**1. Can an interface with default methods still be functional?**  
Yes. It can have many default/static methods, but only one abstract method.

**2. Why do we need `@FunctionalInterface`?**  
For compile-time validation and code clarity.

**3. Can lambda work without a functional interface?**  
No. Lambda needs a target functional interface type.

**4. Why are local variables in lambdas effectively final?**  
To avoid unsafe mutation of captured local state.

**5. When should I use `Function` vs `Consumer`?**  
Use `Function` when you return a value; use `Consumer` when you only perform an action.

---

## 10. Final Takeaways

- Java 8 made Java more concise and expressive.
- Functional interfaces are the base of lambdas.
- `Predicate`, `Function`, `Consumer`, and `Supplier` are the most important built-ins.
- Lambda expressions reduce boilerplate and are heavily used in modern backend Java.
- Method references improve readability when the lambda only forwards a method call.
- Effectively final variables are a common interview favorite.

