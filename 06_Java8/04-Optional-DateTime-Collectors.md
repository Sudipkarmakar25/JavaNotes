# Java 8 Optional and Collectors

> **Scope Note:** Java 8 Date-Time API is covered separately in `05-Date-Time-API.md`.

## 1. Optional

### Short Definition
`Optional<T>` is a container object that may contain a value or may be empty.

### Why Optional
- Helps reduce accidental `NullPointerException`.
- Makes absence of value explicit in APIs.
- Encourages safer null handling than manual `if (x != null)` checks everywhere.

### Simple Example
```java
class Demo {
	void test() {
		Optional<String> name = Optional.of("Amit");
		System.out.println(name.get());
	}
}
```

### Interview Notes
- `Optional` is mainly for **return types**, not for entity fields or method parameters in most real projects.
- It improves readability, but overusing it can make code noisy.

---

## 2. Creating Optional

### 2.1 `of()`
**Purpose:** Creates an `Optional` with a non-null value.  
**Example:**
```java
class Demo {
	void test() {
		Optional<String> name = Optional.of("Java");
	}
}
```
**Interview Note:** If the value is `null`, `of()` throws `NullPointerException`.

### 2.2 `ofNullable()`
**Purpose:** Creates an `Optional` that may hold a null or non-null value.  
**Example:**
```java
class Demo {
	void test(String value) {
		Optional<String> name = Optional.ofNullable(value);
	}
}
```
**Interview Note:** Use this when the source may be null.

### 2.3 `empty()`
**Purpose:** Returns an empty `Optional`.  
**Example:**
```java
class Demo {
	void test() {
		Optional<String> name = Optional.empty();
	}
}
```
**Interview Note:** Represents “no value present”.

---

## 3. Reading and Handling Optional Values

### 3.1 `get()`
**Purpose:** Returns the value if present.  
**Example:**
```java
class Demo {
	void test() {
		Optional<String> name = Optional.of("Spring");
		System.out.println(name.get());
	}
}
```
**Interview Note:** Avoid `get()` unless you are sure the value exists; otherwise it throws `NoSuchElementException`.

### 3.2 `orElse()`
**Purpose:** Returns the value if present, otherwise returns a default value.  
**Example:**
```java
class Demo {
	void test(String input) {
		String name = Optional.ofNullable(input).orElse("Default");
	}
}
```
**Interview Note:** The default value is created eagerly.

### 3.3 `orElseGet()`
**Purpose:** Returns the value if present, otherwise computes a default value lazily.  
**Example:**
```java
class Demo {
	void test(String input) {
		String name = Optional.ofNullable(input).orElseGet(() -> "Generated");
	}
}
```
**Interview Note:** Prefer `orElseGet()` when the fallback is expensive.

### 3.4 `orElseThrow()`
**Purpose:** Throws an exception if the value is absent.  
**Example:**
```java
class Demo {
	void test(String input) {
		String name = Optional.ofNullable(input)
				.orElseThrow(() -> new IllegalArgumentException("Name not found"));
	}
}
```
**Interview Note:** Common in service-layer validation and lookup methods.

### 3.5 `ifPresent()`
**Purpose:** Executes logic only if a value exists.  
**Example:**
```java
class Demo {
	void test() {
		Optional.of("Java").ifPresent(System.out::println);
	}
}
```
**Interview Note:** Good for small side effects, but avoid putting too much logic inside it.

---

## 4. Transforming Optional Values

### 4.1 `map()`
**Purpose:** Transforms the wrapped value if present.  
**Example:**
```java
class Demo {
	void test() {
		Optional<Integer> length = Optional.of("Java")
				.map(String::length);
	}
}
```
**Interview Note:** Use `map()` for one-to-one transformation.

### 4.2 `flatMap()`
**Purpose:** Transforms the value when the mapping function itself returns an `Optional`.  
**Example:**
```java
class User {
	Optional<String> getEmail() {
		return Optional.of("a@x.com");
	}
}

class Demo {
	void test() {
		Optional<String> email = Optional.of(new User())
				.flatMap(User::getEmail);
	}
}
```
**Interview Note:** Prevents nested `Optional<Optional<T>>`.

### `map()` vs `flatMap()`
| Method | Use When | Result |
|---|---|---|
| `map()` | function returns normal value | `Optional<R>` |
| `flatMap()` | function returns `Optional<R>` | flattened `Optional<R>` |

---

## 5. Quick Optional Example

```java
class Profile {
	String getEmail() {
		return "mail@example.com";
	}
}

class User {
	Profile getProfile() {
		return new Profile();
	}
}

class Demo {
	void test(User user) {
		String email = Optional.ofNullable(user)
				.map(User::getProfile)
				.map(Profile::getEmail)
				.orElse("not-available@example.com");
	}
}
```

### Interview Notes
- This is cleaner than multiple nested null checks.
- Good for read chains, but do not force `Optional` everywhere.

---

## 6. Collectors

### Short Definition
`Collectors` is a utility class used with `Stream.collect()` to gather stream results into collections, maps, grouped results, or summary values.

### Simple Example
```java
class Demo {
	void test() {
		List<String> result = Stream.of("a", "b", "c")
				.map(String::toUpperCase)
				.collect(Collectors.toList());
	}
}
```

### Interview Notes
- `collect()` is one of the most used terminal operations in stream-based code.
- `Collectors` helps convert stream pipelines into practical business results.

---

## 7. Common Collectors Methods

### 7.1 `collect()`
**Purpose:** Terminal operation that gathers stream output using a collector.  
**Example:**
```java
class Demo {
	void test() {
		List<String> names = Stream.of("a", "b")
				.collect(Collectors.toList());
	}
}
```
**Interview Note:** `collect()` is the entry point; `Collectors` defines how results are accumulated.

### 7.2 `toList()`
**Purpose:** Collects elements into a `List`.  
**Example:**
```java
class Demo {
	void test() {
		List<String> names = Stream.of("A", "B")
				.collect(Collectors.toList());
	}
}
```
**Interview Note:** Most common collector for returning filtered or mapped results.

### 7.3 `toSet()`
**Purpose:** Collects elements into a `Set`.  
**Example:**
```java
class Demo {
	void test() {
		Set<Integer> nums = Stream.of(1, 1, 2, 3)
				.collect(Collectors.toSet());
	}
}
```
**Interview Note:** Useful when uniqueness matters; order is not guaranteed by default.

### 7.4 `toMap()`
**Purpose:** Collects elements into a `Map`.  
**Example:**
```java
class Demo {
	void test() {
		Map<Integer, String> map = Stream.of("A", "BB", "C")
				.collect(Collectors.toMap(String::length, s -> s, (a, b) -> a));
	}
}
```
**Interview Note:** Duplicate keys cause issues unless you provide a merge function.

### 7.5 `groupingBy()`
**Purpose:** Groups elements by a classifier.  
**Example:**
```java
class Demo {
	void test() {
		Map<Integer, List<String>> grouped = Stream.of("java", "api", "spring")
				.collect(Collectors.groupingBy(String::length));
	}
}
```
**Interview Note:** Common in reporting, aggregation, and analytics use cases.

### 7.6 `partitioningBy()`
**Purpose:** Splits elements into two groups based on a boolean condition.  
**Example:**
```java
class Demo {
	void test() {
		Map<Boolean, List<Integer>> result = Stream.of(1, 2, 3, 4)
				.collect(Collectors.partitioningBy(n -> n % 2 == 0));
	}
}
```
**Interview Note:** Always creates exactly two groups: `true` and `false`.

### 7.7 `joining()`
**Purpose:** Concatenates string elements into one string.  
**Example:**
```java
class Demo {
	void test() {
		String text = Stream.of("Java", "Spring", "Boot")
				.collect(Collectors.joining(", "));
	}
}
```
**Interview Note:** Useful for display strings, CSV-like output, and logging.

### 7.8 `counting()`
**Purpose:** Counts elements as a collector result.  
**Example:**
```java
class Demo {
	void test() {
		Long count = Stream.of("a", "b", "c")
				.collect(Collectors.counting());
	}
}
```
**Interview Note:** Often used inside `groupingBy()` for grouped counts.

---

## 8. Quick Collectors Examples

### Group Users by Department
```java
class Employee {
	String getDepartment() {
		return "IT";
	}
}

class Demo {
	void test(List<Employee> employees) {
		Map<String, List<Employee>> byDept = employees.stream()
				.collect(Collectors.groupingBy(Employee::getDepartment));
	}
}
```

### Count Users by Role
```java
class User {
	String getRole() {
		return "ADMIN";
	}
}

class Demo {
	void test(List<User> users) {
		Map<String, Long> roleCount = users.stream()
				.collect(Collectors.groupingBy(User::getRole, Collectors.counting()));
	}
}
```

### Join Names
```java
class User {
	String getName() {
		return "Amit";
	}
}

class Demo {
	void test(List<User> users) {
		String names = users.stream()
				.map(User::getName)
				.collect(Collectors.joining(", "));
	}
}
```

---

## 9. Common Optional and Collectors Interview Questions

### 1. Why was `Optional` introduced?
To make absence of value explicit and reduce null-related bugs.

### 2. What is the difference between `of()` and `ofNullable()`?
`of()` does not allow null; `ofNullable()` allows null and returns empty if null.

### 3. Why is `get()` discouraged?
Because it throws an exception if the value is absent.

### 4. Difference between `orElse()` and `orElseGet()`?
`orElse()` creates the fallback immediately; `orElseGet()` creates it lazily.

### 5. When should `orElseThrow()` be used?
When missing value is a business error and should fail fast.

### 6. Difference between `map()` and `flatMap()` in `Optional`?
`map()` wraps normal values; `flatMap()` avoids nested `Optional`.

### 7. Should `Optional` be used in entity fields?
Usually no; it is mainly preferred for return types.

### 8. What does `collect()` do?
It converts a stream into a final result using a collector.

### 9. When do we use `groupingBy()`?
When data needs to be grouped by a key like department, age, or category.

### 10. Difference between `groupingBy()` and `partitioningBy()`?
`groupingBy()` can create many groups; `partitioningBy()` creates only `true` and `false` groups.

### 11. Why can `toMap()` fail?
Because duplicate keys can throw an exception if no merge function is provided.

### 12. When is `joining()` useful?
When converting multiple strings into one readable output.

### 13. Why use `counting()` when `count()` already exists?
`counting()` is useful inside collector-based group operations.

---

## 10. Final Takeaways

- Use `Optional` for safer return values and null handling.
- Prefer `orElseGet()` over `orElse()` when fallback creation is expensive.
- Avoid overusing `get()`.
- `Collectors` turns streams into practical results like lists, maps, grouped data, and joined strings.
- `groupingBy()`, `toMap()`, and `partitioningBy()` are common interview favorites.

