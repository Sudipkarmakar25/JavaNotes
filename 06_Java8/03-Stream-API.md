# Java 8 Stream API

## 1. What is Stream

### Short Definition
A **Stream** is a pipeline for processing data from a source like a collection, array, or file.

### Important Points
- Stream does **not store data**.
- It processes data in a **declarative** style.
- It is usually **single-use**.
- It can be **sequential** or **parallel**.

### Simple Example
```
class Demo {
	void test() {
		List<String> names = Arrays.asList("Amit", "Rahul", "Anu");

		names.stream()
				.filter(name -> name.startsWith("A"))
				.forEach(System.out::println);
	}
}
```

### Interview Notes
- Collection = storage, Stream = processing.
- Streams are best for filtering, transformation, and aggregation.

---

## 2. Stream Pipeline

### Short Definition
A stream pipeline is the full chain of operations applied to data.

```text
Source -> Intermediate Operations -> Terminal Operation
```

### Example
```
class Demo {
	void test() {
		List<String> names = Arrays.asList("Amit", "Rahul", "Anu");

		List<String> result = names.stream()
				.filter(s -> s.length() > 3)
				.map(String::toUpperCase)
				.collect(Collectors.toList());
	}
}
```

### Interview Notes
- Intermediate operations build the pipeline.
- Terminal operation triggers execution.

---

## 3. Creating Streams

### Short Definition
Streams can be created from collections, arrays, fixed values, or generator methods.

### Small Examples

#### From Collection
```
class Demo {
	void test() {
		Stream<Integer> s1 = Arrays.asList(1, 2, 3).stream();
	}
}
```

#### From Array
```
class Demo {
	void test() {
		IntStream s2 = Arrays.stream(new int[]{1, 2, 3});
	}
}
```

#### Using `Stream.of()`
```
class Demo {
	void test() {
		Stream<String> s3 = Stream.of("a", "b", "c");
	}
}
```

#### Using `Stream.generate()`
```
class Demo {
	void test() {
		Stream<Double> s4 = Stream.generate(Math::random).limit(3);
	}
}
```

#### Using `Stream.iterate()`
```
class Demo {
	void test() {
		Stream<Integer> s5 = Stream.iterate(1, n -> n + 1).limit(5);
	}
}
```

### Interview Notes
- Prefer `IntStream`, `LongStream`, `DoubleStream` for numeric performance.
- Infinite streams should usually be combined with `limit()`.

---

## 4. Intermediate Operations

### Short Definition
Intermediate operations transform a stream and return another stream.

### Important Points
- They are **lazy**.
- They do not run until a terminal operation is called.

| Operation | One-line Definition | Syntax |
|---|---|---|
| `filter` | Keeps elements matching a condition | `stream.filter(p)` |
| `map` | Transforms each element | `stream.map(f)` |
| `flatMap` | Flattens nested structures | `stream.flatMap(f)` |
| `distinct` | Removes duplicates | `stream.distinct()` |
| `sorted` | Sorts elements | `stream.sorted()` |
| `limit` | Keeps first `n` elements | `stream.limit(n)` |
| `skip` | Skips first `n` elements | `stream.skip(n)` |

### Small Examples

#### `filter`
```
class Demo {
	void test() {
		List<Integer> evens = Arrays.asList(1, 2, 3, 4).stream()
				.filter(n -> n % 2 == 0)
				.collect(Collectors.toList());
	}
}
```

#### `map`
```
class Demo {
	void test() {
		List<String> upper = Arrays.asList("java", "spring").stream()
				.map(String::toUpperCase)
				.collect(Collectors.toList());
	}
}
```

#### `flatMap`
```
class Demo {
	void test() {
		List<String> flat = Arrays.asList(Arrays.asList("A", "B"), Arrays.asList("C"))
				.stream()
				.flatMap(List::stream)
				.collect(Collectors.toList());
	}
}
```

#### `distinct`
```
class Demo {
	void test() {
		List<Integer> unique = Arrays.asList(1, 1, 2, 3).stream()
				.distinct()
				.collect(Collectors.toList());
	}
}
```

#### `sorted`
```
class Demo {
	void test() {
		List<Integer> sorted = Arrays.asList(5, 2, 9).stream()
				.sorted()
				.collect(Collectors.toList());
	}
}
```

#### `limit`
```
class Demo {
	void test() {
		List<Integer> firstTwo = Arrays.asList(10, 20, 30, 40).stream()
				.limit(2)
				.collect(Collectors.toList());
	}
}
```

#### `skip`
```
class Demo {
	void test() {
		List<Integer> rest = Arrays.asList(10, 20, 30, 40).stream()
				.skip(2)
				.collect(Collectors.toList());
	}
}
```

### Interview Notes
- `map()` is one-to-one transformation.
- `flatMap()` is used when the input contains nested collections.
- `distinct()` depends on `equals()` and `hashCode()` for objects.

---

## 5. Terminal Operations

### Short Definition
Terminal operations produce the final result and consume the stream.

| Operation | One-line Definition | Syntax |
|---|---|---|
| `collect` | Gathers result into a collection/object | `stream.collect(...)` |
| `reduce` | Combines elements into one value | `stream.reduce(...)` |
| `count` | Returns number of elements | `stream.count()` |
| `min` | Returns smallest element | `stream.min(cmp)` |
| `max` | Returns largest element | `stream.max(cmp)` |
| `findFirst` | Returns first element | `stream.findFirst()` |
| `findAny` | Returns any one element | `stream.findAny()` |

### Small Examples

#### `collect`
```
class Demo {
	void test() {
		List<String> result = Arrays.asList("a", "b").stream()
				.map(String::toUpperCase)
				.collect(Collectors.toList());
	}
}
```

#### `reduce`
```
class Demo {
	void test() {
		int sum = Arrays.asList(1, 2, 3, 4).stream()
				.reduce(0, Integer::sum);
	}
}
```

#### `count`
```
class Demo {
	void test() {
		long count = Arrays.asList("a", "bb", "ccc").stream()
				.filter(s -> s.length() > 1)
				.count();
	}
}
```

#### `min`
```
class Demo {
	void test() {
		Optional<Integer> min = Arrays.asList(4, 2, 9).stream()
				.min(Integer::compareTo);
	}
}
```

#### `max`
```
class Demo {
	void test() {
		Optional<Integer> max = Arrays.asList(4, 2, 9).stream()
				.max(Integer::compareTo);
	}
}
```

#### `findFirst`
```
class Demo {
	void test() {
		Optional<String> first = Arrays.asList("A", "B", "C").stream()
				.findFirst();
	}
}
```

#### `findAny`
```
class Demo {
	void test() {
		Optional<String> any = Arrays.asList("A", "B", "C").stream()
				.findAny();
	}
}
```

### Interview Notes
- `collect()` is used most in business code.
- `reduce()` is for totals and aggregation.
- `findAny()` is especially relevant in parallel streams.

---

## 6. Lazy Evaluation

### Short Definition
Intermediate operations run only when a terminal operation is invoked.

### Example
```
class Demo {
	void test() {
		Arrays.asList(1, 2, 3, 4).stream()
				.filter(n -> {
					System.out.println("Checking " + n);
					return n % 2 == 0;
				})
				.findFirst();
	}
}
```

### Important Points
- Improves efficiency.
- Enables short-circuiting with operations like `findFirst()` and `limit()`.

### Interview Notes
- Streams are lazy by design; that is why building pipelines is cheap.

---

## 7. Parallel Streams

### Short Definition
Parallel streams process stream operations using multiple threads.

### Syntax
```text
list.parallelStream()
list.stream().parallel()
```

### Example
```
class Demo {
	void test() {
		int sum = Arrays.asList(1, 2, 3, 4, 5).parallelStream()
				.reduce(0, Integer::sum);
	}
}
```

### Important Points
- Good for CPU-heavy bulk processing.
- Not always faster for small datasets.
- Avoid shared mutable state.
- Order-sensitive operations may reduce benefit.

### Interview Notes
- Use parallel streams only after measuring performance.

---

## 8. Stream vs Collection

| Point | Stream | Collection |
|---|---|---|
| Purpose | Process data | Store data |
| Traversal | One-time | Multiple times |
| Style | Declarative | Data structure oriented |
| Modification | No direct storage change | Supports add/remove |

### Example
- `List<User>` stores users.
- `users.stream().filter(...).map(...)` processes users.

### Interview Notes
- Stream is not a data structure.

---

## 9. Quick Production Examples

### Filter + Map + Collect
```
class Demo {
	void test() {
		List<String> result = Arrays.asList("amit", "rahul", "ajay").stream()
				.filter(name -> name.startsWith("a"))
				.map(String::toUpperCase)
				.collect(Collectors.toList());
	}
}
```

### Flatten Nested Data
```
class Customer {
	List<String> getPhoneNumbers() {
		return Arrays.asList("111", "222");
	}
}

class Demo {
	void test(List<Customer> customers) {
		List<String> phones = customers.stream()
				.flatMap(c -> c.getPhoneNumbers().stream())
				.collect(Collectors.toList());
	}
}
```

### Total Amount
```
class Order {
	int getAmount() {
		return 100;
	}
}

class Demo {
	void test(List<Order> orders) {
		int total = orders.stream()
				.map(Order::getAmount)
				.reduce(0, Integer::sum);
	}
}
```

---

## 10. Top 15 Stream Interview Questions

1. **What is Stream API?**  
   A declarative API for processing data from sources like collections.

2. **Does a stream store data?**  
   No, it only processes data.

3. **What is a stream pipeline?**  
   Source + intermediate operations + terminal operation.

4. **Difference between `map()` and `flatMap()`?**  
   `map()` transforms; `flatMap()` transforms and flattens.

5. **Why are streams lazy?**  
   Intermediate operations run only when a terminal operation is called.

6. **Can a stream be reused?**  
   No, it is generally single-use.

7. **Difference between `findFirst()` and `findAny()`?**  
   `findFirst()` respects order; `findAny()` may return any element.

8. **Difference between `collect()` and `reduce()`?**  
   `collect()` gathers into a result container; `reduce()` returns one combined value.

9. **What does `distinct()` depend on?**  
   `equals()` and `hashCode()`.

10. **Why use `IntStream`?**  
   To avoid boxing/unboxing overhead.

11. **Are streams always faster than loops?**  
   No, readability is often the main benefit.

12. **When should parallel streams be avoided?**  
   Small datasets, I/O-heavy tasks, shared mutable state.

13. **Can streams modify the original collection?**  
   Usually no; they create processed results.

14. **What is short-circuiting?**  
   Early termination by operations like `findFirst()` or `limit()`.

15. **Difference between Collection and Stream?**  
   Collection stores data; Stream processes data.

---

## 11. Final Takeaways

- Remember the model: **source -> intermediate -> terminal**.
- Practice `filter`, `map`, `flatMap`, `collect`, and `reduce` first.
- Understand lazy evaluation and parallel stream tradeoffs for interviews.

