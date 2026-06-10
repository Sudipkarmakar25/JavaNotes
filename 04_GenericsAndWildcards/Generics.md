# Java Generics – Complete Guide

---

# 1️⃣ What Are Generics?

**Generics** allow classes, interfaces, and methods to operate on **different data types while providing compile-time type safety**.

They enable **type parameterization**, meaning a class or method can work with any object type.

Example:

```java
List<String> names = new ArrayList<>();
```

Here:

`String` is the **generic type parameter**.

---

# 2️⃣ Why Generics Were Introduced

Before Java 5, collections stored objects as:

```java
List list = new ArrayList();
list.add("Hello");
list.add(10);
```

Problems:

- No type safety
- Runtime errors
- Manual casting required

Example:

```java
String s = (String) list.get(0);
```

Generics solved this by enforcing **compile-time type checking**.

---

# 3️⃣ Generic Class

A class can define a **type parameter**.

Example:

```java
class Box<T> {

    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

Usage:

```java
Box<Integer> intBox = new Box<>();
intBox.set(10);

Box<String> strBox = new Box<>();
strBox.set("Hello");
```

Here `T` is a **type parameter**.

---

# 4️⃣ Generic Methods

Methods can also define generic types.

Example:

```java
public class Util {

    public static <T> void printValue(T value) {
        System.out.println(value);
    }
}
```

Usage:

```java
Util.printValue(10);
Util.printValue("Hello");
```

The compiler automatically infers the type.

---

# 5️⃣ Multiple Type Parameters

Generics can use multiple types.

Example:

```java
class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

Usage:

```java
Pair<String, Integer> p = new Pair<>("Age", 25);
```

---

# 6️⃣ Generic Interfaces

Interfaces can also use generics.

Example:

```java
interface Repository<T> {

    void save(T entity);

    T findById(int id);
}
```

Implementation:

```java
class UserRepository implements Repository<User> {

    public void save(User user) {
    }

    public User findById(int id) {
        return null;
    }
}
```

---

# 7️⃣ Type Erasure (Important Concept)

Generics exist only at **compile time**.

During compilation, type parameters are removed.

Example:

```java
List<String>
```

Becomes:

```java
List
```

This is called **Type Erasure**.

Because of this:

- Generics do not exist at runtime
- Cannot use primitives like `int`

Example:

```java
List<int> ❌
List<Integer> ✅
```

---

# 8️⃣ Bounded Generics

Sometimes we restrict types using **bounds**.

Example:

```java
class Calculator<T extends Number> {

    private T value;

    public Calculator(T value) {
        this.value = value;
    }
}
```

Now only subclasses of `Number` allowed:

```java
Calculator<Integer> c1 = new Calculator<>(10);
Calculator<Double> c2 = new Calculator<>(5.5);
```

Not allowed:

```java
Calculator<String> ❌
```

---

# 9️⃣ Wildcards in Generics

Wildcards are used when type is **unknown**.

Symbol:

```
?
```

Example:

```java
List<?> list;
```

Means list of **unknown type**.

---

# 🔟 Upper Bounded Wildcard

Restricts types to subclasses.

Example:

```java
List<? extends Number>
```

Accepts:

- Integer
- Double
- Float

Example:

```java
void printNumbers(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
}
```

---

# 1️⃣1️⃣ Lower Bounded Wildcard

Allows types that are **superclasses**.

Example:

```java
List<? super Integer>
```

Accepts:

- Integer
- Number
- Object

Used when **adding elements**.

---

# 1️⃣2️⃣ PECS Rule (Very Important)

Rule:

**PECS = Producer Extends, Consumer Super**

Meaning:

| Case | Use |
|-----|-----|
| Reading values | `extends` |
| Adding values | `super` |

Example:

Producer:

```java
List<? extends Number>
```

Consumer:

```java
List<? super Integer>
```

---

# 1️⃣3️⃣ Generics with Collections

Example:

```java
List<String> names = new ArrayList<>();

names.add("Rahul");
names.add("Amit");
```

Advantages:

- Compile-time safety
- No casting needed

---

# 1️⃣4️⃣ Advantages of Generics

- Type safety
- Eliminates casting
- Code reusability
- Compile-time error detection
- Cleaner APIs

---

# 1️⃣5️⃣ Limitations of Generics

- Cannot use primitives
- Cannot create generic arrays

Example:

```java
new T[] ❌
```

- Cannot use `instanceof` with generics

Example:

```java
if(obj instanceof List<String>) ❌
```

---

# 1️⃣6️⃣ Real-World Uses

Generics are heavily used in:

- Collections (`List<T>`, `Map<K,V>`)
- Spring Data repositories
- Optional<T>
- Streams API
- Functional interfaces

Example:

```java
public interface JpaRepository<T, ID>
```

---

# 1️⃣7️⃣ Interview Questions

- What are generics?
- Why were generics introduced?
- What is type erasure?
- Difference between `extends` and `super`?
- What is PECS rule?
- Can generics work with primitives?
- Why generic arrays are not allowed?

---

# 1️⃣8️⃣ Interview Summary (≈30 words)

Generics enable type-safe programming in Java by allowing classes and methods to operate on parameterized types, eliminating casting and detecting type errors at compile time while supporting reusable and flexible code design.

---