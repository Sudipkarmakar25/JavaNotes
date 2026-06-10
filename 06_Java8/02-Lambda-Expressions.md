# Java 8 Lambda Expressions

## 1. What is a Lambda Expression

### Short Definition
A lambda expression is a concise way to provide the implementation of a functional interface.

### Why It Matters
- Reduces boilerplate code.
- Makes callback-style code cleaner.
- Used heavily with Streams, collections, and event-driven logic.
- Enables functional-style programming in Java.

### Simple Example
```java
class Demo {
	void test() {
		Runnable task = () -> System.out.println("Running");
		task.run();
	}
}
```

### Interview Note
- Lambda is not an independent type; it needs a functional interface target.

---

## 2. Basic Syntax

```java
(parameters) -> expression
(parameters) -> { statements; }
```

### Common Forms

#### No Parameter
```java
class Demo {
	void test() {
		Runnable r = () -> System.out.println("Hello");
	}
}
```

#### Single Parameter
```java
class Demo {
	void test() {
		Consumer<String> c = name -> System.out.println(name);
	}
}
```

#### Multiple Parameters
```java
class Demo {
	void test() {
		Comparator<Integer> cmp = (a, b) -> a.compareTo(b);
	}
}
```

#### Block Body
```java
class Demo {
	void test() {
		Function<Integer, Integer> square = n -> {
			int result = n * n;
			return result;
		};
	}
}
```

### Interview Note
- Parentheses are optional only for one parameter.
- `return` is optional only in single-expression lambdas.

---

## 3. Lambda and Functional Interface

### Short Definition
Lambda works only when the target type is a functional interface.

### Example
```java
@FunctionalInterface
interface Calculator {
	int add(int a, int b);
}

class Demo {
	void test() {
		Calculator c = (a, b) -> a + b;
		System.out.println(c.add(2, 3));
	}
}
```

### Interview Note
- The compiler uses the target interface method signature to understand the lambda.

---

## 4. Type Inference

### Short Definition
Java can infer parameter and return types of lambdas from context.

### Example
```java
class Demo {
	void test() {
		Comparator<String> cmp = (a, b) -> a.compareToIgnoreCase(b);
	}
}
```

### Interview Note
- You usually do not write parameter types inside lambdas unless clarity is needed.

---

## 5. Lambda vs Anonymous Class

| Point | Lambda | Anonymous Class |
|---|---|---|
| Boilerplate | Less | More |
| `this` | Refers to outer object | Refers to anonymous object |
| Use case | Small functional behavior | More verbose custom logic |
| Target | Functional interface only | Interface or abstract class |

### Example
```java
class Demo {
	void test() {
		Runnable r1 = new Runnable() {
			@Override
			public void run() {
				System.out.println("Anonymous");
			}
		};

		Runnable r2 = () -> System.out.println("Lambda");
	}
}
```

### Interview Note
- In lambda, `this` points to the enclosing class, not a new inner object.

---

## 6. Effectively Final Variables

### Short Definition
Local variables used inside a lambda must be final or effectively final.

### Valid Example
```java
class Demo {
	void test() {
		int base = 10;
		Function<Integer, Integer> addBase = n -> n + base;
	}
}
```

### Invalid Example
```java
class Demo {
	void test() {
		int base = 10;
		base++;
		Function<Integer, Integer> addBase = n -> n + base; // compile-time error
	}
}
```

### Interview Note
- Java restricts mutation of captured local variables to keep lambda behavior safe and predictable.

---

## 7. Method References

### Short Definition
Method reference is a shorter form of lambda when the lambda only calls an existing method.

### Common Forms
- `ClassName::staticMethod`
- `objectRef::instanceMethod`
- `ClassName::instanceMethod`
- `ClassName::new`

### Example
```java
class Demo {
	void test() {
		Consumer<String> printer = System.out::println;
		printer.accept("Java");
	}
}
```

### Interview Note
- Method reference improves readability, but use it only when it is clearer than a lambda.

---

## 8. Common Production Uses

- Sorting collections
- Stream filtering and mapping
- Callback implementations
- Event handling
- Small validation or transformation logic

### Example
```java
class Demo {
	void test() {
		List<String> names = Arrays.asList("Rahul", "Amit", "Anu");
		names.sort((a, b) -> a.compareToIgnoreCase(b));
	}
}
```

### Interview Note
- Lambdas are best for small behavior; if logic grows large, move it to a method.

---

## 9. Internal Working Basics

### Short Definition
Lambdas are implemented using runtime support like `invokedynamic` instead of always generating a normal anonymous inner class in source style.

### Important Points
- More lightweight than many anonymous-class use cases.
- JVM links lambda implementation dynamically.
- Helps improve readability without changing Java's core object model.

### Interview Note
- Common answer: lambda is not exactly “just anonymous class syntax”; internally the JVM handles it differently.

---

## 10. Top Lambda Interview Questions

### 1. Can lambda be used without a functional interface?
No. It needs a target functional interface.

### 2. Why are local variables effectively final in lambdas?
To avoid unsafe mutation of captured local state.

### 3. What is the difference between lambda and anonymous class?
Lambda is shorter and `this` refers to the outer object; anonymous class creates its own inner object context.

### 4. Can lambda access instance variables?
Yes. Instance and static variables can be accessed normally.

### 5. Can lambda throw exceptions?
Yes, but checked exceptions must still follow the functional interface method contract.

### 6. Why are lambdas important in Java 8?
They reduce boilerplate and work naturally with Streams and functional interfaces.

### 7. Is method reference different from lambda?
It is just a shorter form when a lambda directly calls an existing method.

---

## 11. Final Takeaways

- Lambda expressions make Java code shorter and cleaner.
- They work only with functional interfaces.
- Understand syntax, variable capture, and `this` behavior for interviews.
- Learn method references and effectively final variables well.

