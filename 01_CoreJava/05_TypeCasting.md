# Type Casting in Java

## What Is Type Casting?
Type casting means converting a value from one type to another.

In Java, this happens in two major forms:
- **Primitive casting**
- **Reference casting**

---

## Primitive Type Casting
### 1. Widening Casting (implicit)
Small type -> bigger compatible type.

```java
int a = 10;
long b = a;
double c = b;
```

Why implicit?
Because the destination type can safely represent the source range in normal language rules.

### 2. Narrowing Casting (explicit)
Bigger type -> smaller type.

```java
double x = 10.9;
int y = (int) x; // 10
```

Narrowing may cause:
- data loss
- overflow
- truncation

---

## Numeric Casting Rules
### Decimal to integer
Fractional part is discarded, not rounded.

```java
int x = (int) 5.99; // 5
```

### Overflow example
```java
int x = 130;
byte b = (byte) x; // overflowed result
```

This happens because only lower bits remain after conversion.

---

## Type Promotion in Expressions
Java promotes smaller integer types in arithmetic.

```java
byte a = 10;
byte b = 20;
int c = a + b;
```

Even though both operands are `byte`, result becomes `int`.

---

## `char` and Numeric Casting
`char` is a 16-bit unsigned Unicode code unit.

```java
char ch = 'A';
int code = ch; // 65
char again = (char) code;
```

---

## Reference Type Casting
Used with objects in inheritance hierarchies.

### Upcasting
Child object -> parent reference.
Implicit and safe.

```java
Dog d = new Dog();
Animal a = d;
```

### Downcasting
Parent reference -> child reference.
Explicit and only safe if object really is that child type.

```java
Animal a = new Dog();
Dog d = (Dog) a;
```

Unsafe downcast causes `ClassCastException`.

---

## `instanceof` Before Downcasting
```java
if (a instanceof Dog) {
    Dog d = (Dog) a;
}
```

This avoids invalid casts.

---

## Compile-Time vs Runtime in Casting
- some invalid casts are caught by compiler
- some compile but fail at runtime because actual object type differs

Example:
```java
Animal a = new Cat();
Dog d = (Dog) a; // runtime ClassCastException
```

---

## Important Pitfalls
- narrowing primitive casts may silently lose data
- reference casting does not change the object, only reference view
- `instanceof` checks runtime type, not variable name/type alone
- numeric promotion can surprise in comparisons and arithmetic

---

## Interview-Specific Questions
### Difference between widening and narrowing casting?
Widening converts smaller compatible type to larger type automatically, while narrowing converts larger type to smaller type explicitly and may lose data.

### Why is downcasting risky?
Because the reference may not actually point to an object of the target subtype, causing `ClassCastException`.

### Does casting create a new object?
No. Casting only changes how the existing value/reference is treated.

### Why does `byte + byte` become `int`?
Because Java promotes smaller integer types to `int` during arithmetic operations.

### What is the use of `instanceof`?
It checks whether an object is compatible with a target type before downcasting.

---

## Quick Revision
- Widening = implicit, safer.
- Narrowing = explicit, can lose data.
- Upcasting = safe parent view of child object.
- Downcasting = explicit and runtime-sensitive.
- Casting changes interpretation, not object identity.

