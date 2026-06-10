# String vs StringBuilder vs StringBuffer

## Core Difference
All three deal with character sequences, but their design goals differ.

| Type | Mutable? | Thread-safe? | Typical Use |
|---|---|---|---|
| `String` | No | effectively safe due to immutability | constants, keys, DTO text |
| `StringBuilder` | Yes | No | single-threaded string building |
| `StringBuffer` | Yes | Yes | legacy multi-threaded mutable text |

---

## `String`
`String` is **immutable**.
Once created, its content cannot change.

```java
String s = "Java";
s.concat(" Rocks");
System.out.println(s); // Java
```

Why this matters:
- safe for sharing
- good for caching
- usable as map keys
- enables string pool optimization

### String Pool
String literals are stored in the **string constant pool**.

```java
String a = "hello";
String b = "hello";
System.out.println(a == b); // true
```

But:
```java
String a = new String("hello");
String b = new String("hello");
System.out.println(a == b); // false
```

Use `equals()` for content comparison.

---

## `StringBuilder`
Mutable sequence of characters.
Efficient for repeated append/insert/delete operations.

```java
StringBuilder sb = new StringBuilder();
sb.append("Java").append(" ").append("Notes");
```

### Why faster than repeated `String` concatenation?
Because it avoids creating a new immutable object for every modification.

---

## `StringBuffer`
Same mutable idea as `StringBuilder`, but synchronized.

```java
StringBuffer sb = new StringBuffer();
sb.append("safe");
```

Because its methods are synchronized, it is slower than `StringBuilder` in single-threaded scenarios.

---

## Concatenation and Compiler Optimization
This line:
```java
String s = a + b + c;
```
usually becomes `StringBuilder` usage internally by the compiler for simple expressions.

But in loops, repeated `+` is still a common performance smell.

```java
for (int i = 0; i < n; i++) {
    s += i; // bad for repeated concatenation
}
```

Use `StringBuilder` instead.

---

## `equals()` vs `==`
- `==` compares references
- `equals()` compares content for `String`

```java
String a = new String("java");
String b = new String("java");
System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

---

## `intern()`
`intern()` returns pooled string reference if present; otherwise adds it to the pool.

Useful in niche memory-sharing cases, but not something to use blindly.

---

## When to Use What
### Use `String`
- fixed text
- config values
- keys
- immutable value objects

### Use `StringBuilder`
- loops
- JSON/XML/manual query assembly
- frequent append/modify in one thread

### Use `StringBuffer`
- rare legacy code requiring synchronized mutable buffer
- modern code usually prefers `StringBuilder` + external synchronization if needed

---

## Performance Angle
- `String` modification creates new objects
- `StringBuilder` reuses internal char/byte storage and grows when needed
- `StringBuffer` adds synchronization overhead

Interview-friendly answer:
> `StringBuilder` is generally preferred for mutable string construction unless synchronized mutable access is specifically required.

---

## Interview-Specific Questions
### Why is `String` immutable?
For security, thread safety, string pool reuse, and reliable hashing.

### Difference between `StringBuilder` and `StringBuffer`?
Both are mutable; `StringBuffer` is synchronized and thread-safe, while `StringBuilder` is not synchronized and is faster in single-threaded use.

### Why should we avoid `+` inside loops for strings?
Because it creates many intermediate immutable `String` objects and increases memory/time overhead.

### Why is `String` commonly used as a `HashMap` key?
Because it is immutable, so its hash code remains stable after insertion.

### What does `intern()` do?
It returns the canonical pooled representation of a string.

---

## Quick Revision
- `String` = immutable.
- `StringBuilder` = mutable, fast, not thread-safe.
- `StringBuffer` = mutable, synchronized, slower.
- Use `equals()` for content comparison.
- For repeated concatenation, prefer `StringBuilder`.

