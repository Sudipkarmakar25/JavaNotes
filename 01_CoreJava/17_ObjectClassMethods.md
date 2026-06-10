# `Object` Class Methods

## Why `Object` Matters
`java.lang.Object` is the root class of almost all Java classes.
Every class implicitly inherits from it unless another superclass is specified.

Common methods from `Object` are frequently asked in interviews.

---

## Important Methods
### `toString()`
Returns string representation of object.
Default format is typically class name + hash info.

Override it for readable logs/debugging.

### `equals(Object o)`
Checks logical equality. Default implementation uses reference equality.

### `hashCode()`
Returns integer hash value used by hash-based collections.

### `getClass()`
Returns runtime class object.

### `clone()`
Protected method used for object copying with `Cloneable`.
Default behavior is shallow copy.

### `wait()`, `notify()`, `notifyAll()`
Inter-thread communication methods tied to object monitors.
Must be called within synchronized context on the same object monitor.

### `finalize()`
Deprecated cleanup hook. Avoid in modern Java.

---

## `toString()` Example
```java
@Override
public String toString() {
    return "User{id=" + id + ", name='" + name + "'}";
}
```

Good `toString()` improves debugging and observability.

---

## `wait()` / `notify()` Essentials
These methods are not thread methods; they belong to `Object` because every object can act as a monitor lock.

```java
synchronized (lock) {
    lock.wait();
}
```

```java
synchronized (lock) {
    lock.notify();
}
```

Calling them without owning the monitor causes `IllegalMonitorStateException`.

---

## `getClass()` vs `instanceof`
- `getClass()` gives exact runtime type
- `instanceof` checks compatibility in inheritance hierarchy

Often relevant while writing robust `equals()` implementations.

---

## Object Identity vs Equality
Because `Object.equals()` defaults to reference equality, domain classes often override `equals()` and `hashCode()` together.

---

## Interview-Specific Questions
### Why is `wait()` defined in `Object`, not `Thread`?
Because waiting and notification are monitor-based operations on shared objects, not intrinsic behaviors of the thread class itself.

### Why should we override `hashCode()` when overriding `equals()`?
Because hash-based collections depend on both methods consistently.

### What is the default implementation of `toString()`?
A string containing class name and hash-like hexadecimal identity representation.

### What does `getClass()` return?
The runtime `Class<?>` object representing the actual object type.

### Is `finalize()` recommended?
No. It is deprecated and unreliable for resource cleanup.

---

## Quick Revision
- `Object` is the root of Java class hierarchy.
- Most important methods: `toString`, `equals`, `hashCode`, `getClass`, `wait/notify`.
- `wait/notify` work with object monitors.
- `clone()` is shallow by default.
- `finalize()` should be avoided.

