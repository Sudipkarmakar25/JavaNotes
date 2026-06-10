# `equals()` and `hashCode()`

## Why These Methods Matter
They define **object equality** and are critical for collections like:
- `HashMap`
- `HashSet`
- `LinkedHashMap`
- `ConcurrentHashMap`

If implemented incorrectly, collections behave unexpectedly.

---

## `equals()`
Determines logical equality.

Default implementation from `Object` compares identity (same object reference).

```java
User a = new User(1);
User b = new User(1);
```
Default `equals()` says these are different unless overridden.

---

## `hashCode()`
Returns an integer hash used by hash-based collections to locate buckets efficiently.

Equal objects **must** return the same hash code.

---

## Contract Rules
### `equals()` contract
- reflexive
- symmetric
- transitive
- consistent
- `x.equals(null)` must return `false`

### `hashCode()` contract
- if two objects are equal, they must have same hash code
- unequal objects may still have same hash code (collision allowed)

---

## Why Both Must Be Overridden Together
If you override only `equals()` and not `hashCode()`, hash-based collections break logical lookup behavior.

Example symptom:
- object inserted into `HashSet`
- logically same object not found later

---

## Typical Implementation Pattern
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return id == user.id && Objects.equals(name, user.name);
}

@Override
public int hashCode() {
    return Objects.hash(id, name);
}
```

---

## `instanceof` vs `getClass()` in `equals()`
### Using `instanceof`
Allows equality across subclass boundary if designed that way.

### Using `getClass()`
Requires exact same runtime type.

Interview-friendly point:
> `getClass()` is stricter; `instanceof` can be more flexible but may complicate symmetry in inheritance.

---

## Mutable Fields Problem
Do not use mutable fields in `equals()`/`hashCode()` if object will be used as key in hash-based collections.

Why?
Because changing such field changes hash/equality after insertion, making lookup fail.

---

## Identity vs Logical Equality
- `==` compares references
- `equals()` compares logical state if overridden

Example:
```java
String a = new String("x");
String b = new String("x");
```
`a == b` is false, but `a.equals(b)` is true.

---

## Records and Equality
Records automatically generate `equals()` and `hashCode()` based on components, which is one reason they are good for value types.

---

## Interview-Specific Questions
### Why do we override `hashCode()` when overriding `equals()`?
Because hash-based collections first use hash code for bucket selection and then use `equals()` for final matching.

### Can two unequal objects have same hash code?
Yes. That is called a collision.

### Can two equal objects have different hash codes?
No. That violates the contract.

### What happens if hash code changes after insertion into `HashMap`?
The key may become effectively unreachable through normal lookup.

### Difference between `==` and `equals()`?
`==` checks reference identity, while `equals()` checks logical equality if overridden.

---

## Quick Revision
- `equals()` = logical equality.
- `hashCode()` = hashing support for collections.
- Equal objects must have same hash code.
- Override both together.
- Avoid mutable equality keys.

