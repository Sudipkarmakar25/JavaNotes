# Serialization and Deserialization

## Definitions
- **Serialization** -> converting an object into a byte stream
- **Deserialization** -> reconstructing object state from that byte stream

In core Java, this is commonly done using `ObjectOutputStream` and `ObjectInputStream`.

---

## Why Serialization Is Used
- caching object state
- sending objects over network (legacy/RMI-like cases)
- writing objects to file
- deep copy tricks in some cases

In modern backend systems, JSON/Proto/Avro are often preferred over Java native serialization.

---

## Basic Example
```java
class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
}
```

Only classes implementing `Serializable` participate in default Java serialization.

---

## What Actually Gets Serialized?
- instance fields
- object graph reachable through serializable references

### What does NOT get serialized?
- `static` fields
- `transient` fields
- constructor logic during deserialization

---

## `serialVersionUID`
A version identifier for a serializable class.

```java
private static final long serialVersionUID = 1L;
```

### Why it matters
If sender and receiver class versions are incompatible, deserialization may fail with `InvalidClassException`.

Best practice: explicitly declare it.

---

## `transient`
Marks fields that should not be serialized.

```java
transient String password;
```

Useful for:
- passwords/secrets
- derived data
- non-serializable dependencies
- cache fields

---

## Constructor Behavior
During deserialization:
- constructors of serializable class are not called
- first non-serializable superclass constructor is called

This is a favorite interview question.

---

## Custom Serialization Hooks
You can customize with:
- `private void writeObject(ObjectOutputStream out)`
- `private void readObject(ObjectInputStream in)`
- `readResolve()`
- `writeReplace()`

These allow encryption, validation, singleton control, and custom field handling.

---

## Serialization of Inheritance Hierarchy
- if parent is serializable -> parent fields serialized normally
- if parent is not serializable -> parent no-arg constructor must be available for deserialization path

---

## Security Warning
Java native deserialization is risky when reading untrusted data.
It can be a serious security vulnerability.

Interview-safe statement:
> Avoid native Java deserialization for untrusted input unless carefully controlled.

---

## Serialization vs Externalization
`Externalizable` gives full manual control using:
- `writeExternal`
- `readExternal`

But it increases responsibility and complexity.

---

## Interview-Specific Questions
### What is serialization?
It is the process of converting an object into a byte stream so it can be stored or transferred.

### Why use `serialVersionUID`?
To control version compatibility during deserialization.

### What is `transient`?
It prevents a field from being serialized.

### Are constructors called during deserialization?
Constructors of the serializable class are not called; the first non-serializable superclass constructor is called.

### Is Java serialization still widely preferred?
Not usually for modern external APIs. JSON, Protobuf, and other formats are more common.

---

## Quick Revision
- `Serializable` enables default Java serialization.
- `static` and `transient` fields are not serialized.
- define `serialVersionUID` explicitly.
- native deserialization from untrusted data is dangerous.

