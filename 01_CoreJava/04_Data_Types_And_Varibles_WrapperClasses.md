# Data Types, Variables, and Wrapper Classes

## Java Type System
Java has two broad categories of types:
- **Primitive types** -> store actual values
- **Reference types** -> store references to objects

### Primitive types
- `byte` -> 8-bit signed
- `short` -> 16-bit signed
- `int` -> 32-bit signed
- `long` -> 64-bit signed
- `float` -> 32-bit IEEE 754
- `double` -> 64-bit IEEE 754
- `char` -> 16-bit unsigned Unicode code unit
- `boolean` -> logical true/false

---

## Primitive vs Reference Types
| Aspect | Primitive | Reference |
|---|---|---|
| Stores | actual value | address-like reference |
| Null allowed | no | yes |
| Heap object needed | no | usually yes |
| Default for fields | numeric `0`, `false`, `\u0000` | `null` |

Important: a reference variable is not the object itself.

---

## Variables in Java
### 1. Local Variables
Declared inside methods/blocks.
- no default value
- must be initialized before use
- stored in stack frame logically

### 2. Instance Variables
Declared inside class but outside method.
- belong to object instance
- default values exist
- stored as part of object state on heap

### 3. Static Variables
Declared with `static`.
- belong to class, not object
- one shared copy per class loader

---

## Default Values
For fields only:
- `int` -> `0`
- `double` -> `0.0`
- `boolean` -> `false`
- `char` -> `\u0000`
- reference -> `null`

Local variables do **not** get default values.

---

## Literals and Suffixes
- `10` -> `int`
- `10L` -> `long`
- `10.5` -> `double`
- `10.5F` -> `float`
- `'A'` -> `char`
- `true` -> `boolean`

---

## Wrapper Classes
Each primitive has a corresponding wrapper class:
- `int` -> `Integer`
- `long` -> `Long`
- `double` -> `Double`
- `char` -> `Character`
- `boolean` -> `Boolean`

### Why wrappers exist
- generics work only with reference types
- collections store objects, not primitives
- utility methods like parsing/comparison/constants
- nullability support

---

## Autoboxing and Unboxing
### Autoboxing
Primitive to wrapper automatically.
```java
Integer x = 10;
```

### Unboxing
Wrapper to primitive automatically.
```java
int y = x;
```

### Hidden danger
```java
Integer x = null;
int y = x; // NullPointerException
```

---

## Integer Cache
Java caches certain boxed integer values, typically `-128` to `127`.

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b); // true
```

But:
```java
Integer a = 200;
Integer b = 200;
System.out.println(a == b); // usually false
```

Use `equals()`, not `==`, for wrapper value comparison.

---

## Pass by Value in Java
Java is **always pass-by-value**.
- primitive value is copied
- object reference value is copied

That means Java never passes actual objects by reference in the C++ sense.

---

## Numeric Promotion
In arithmetic expressions, smaller numeric types are promoted.
Example:
```java
byte a = 10;
byte b = 20;
int c = a + b; // result becomes int
```

---

## Wrapper Utility Methods
Examples:
- `Integer.parseInt("123")`
- `Double.parseDouble("10.5")`
- `Integer.valueOf(10)`
- `Character.isDigit('7')`

---

## Interview Traps
- `char` is unsigned 16-bit, not 8-bit.
- `float` needs `F` suffix.
- wrappers are immutable.
- autoboxing can create unnecessary objects.
- unboxing `null` causes `NullPointerException`.

---

## Interview-Specific Questions
### Difference between primitive and wrapper classes?
Primitive types store raw values, while wrapper classes are objects that wrap primitive values and provide utility methods.

### Why do we need wrapper classes?
Because collections and generics require objects, and wrappers also provide parsing and helper utilities.

### What is autoboxing?
Automatic conversion of primitive to wrapper type by the compiler.

### Why is `Integer` comparison using `==` dangerous?
Because `==` compares references, not values, and wrapper caching can make results appear inconsistent.

### Are local variables given default values?
No. They must be explicitly initialized before use.

---

## Quick Revision
- Java types = primitive + reference.
- Fields get default values; local variables do not.
- Wrappers are object forms of primitives.
- Java is always pass-by-value.
- Prefer `equals()` over `==` for wrapper comparison.

