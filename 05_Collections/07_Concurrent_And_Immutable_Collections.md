# Concurrent and Immutable Collections

## 1. Why These Collections Matter
Normal collections like `ArrayList` and `HashMap` are **not thread-safe**.

In multi-threaded backend systems, shared collections can cause:
- race conditions
- lost updates
- inconsistent reads
- `ConcurrentModificationException`

So Java provides:
- concurrent collections
- synchronized wrappers
- immutable collections

---

## 2. Synchronized Collections
Java provides synchronized wrappers through `Collections` utility methods.

### Examples
- `Collections.synchronizedList(...)`
- `Collections.synchronizedSet(...)`
- `Collections.synchronizedMap(...)`

### Characteristics
- thread-safe by synchronization
- simpler but can reduce scalability
- good for basic shared access

### Interview note
These wrappers synchronize access, but high-contention systems usually prefer specialized concurrent collections.

---

## 3. `ConcurrentHashMap`
`ConcurrentHashMap` is the most important concurrent map in Java.

### Characteristics
- thread-safe
- better scalability than `Hashtable`
- concurrent reads and updates
- null keys and values not allowed

### Example
```java
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;

class Demo {
    void run() {
        ConcurrentMap<Integer, String> map = new ConcurrentHashMap<>();
        map.put(1, "A");
        map.putIfAbsent(2, "B");
    }
}
```

### Best use
Shared caches, counters, metadata maps, multi-threaded request processing.

---

## 4. `CopyOnWriteArrayList`
`CopyOnWriteArrayList` creates a new internal copy when modified.

### Characteristics
- very safe iteration
- reads are fast
- writes are expensive
- ideal for read-heavy, write-light workloads

### Example use cases
- configuration snapshots
- listener lists
- mostly-read shared data

### Interview note
Do not use it for write-heavy workloads.

---

## 5. `BlockingQueue` Basics
`BlockingQueue` is used in producer-consumer scenarios.

### Important idea
It can block when:
- queue is empty during take-style operation
- queue is full during put-style operation

### Common implementations
- `ArrayBlockingQueue`
- `LinkedBlockingQueue`

### Production use case
Task pipelines, worker queues, background job processing.

---

## 6. Thread Safety Approaches

| Approach | Idea | Example |
|---|---|---|
| synchronized wrapper | one lock around collection access | `Collections.synchronizedList()` |
| concurrent collection | specialized scalable implementation | `ConcurrentHashMap` |
| immutable collection | no mutation after creation | `List.of()` |

---

## 7. Immutable Collections
Java 9 introduced factory methods for immutable collections.

### Common factories
- `List.of()`
- `Set.of()`
- `Map.of()`

### Characteristics
- cannot add, remove, or replace elements
- safer sharing between components
- good for constants and fixed config data

### Example
```java
import java.util.List;
import java.util.Map;

class Demo {
    void run() {
        List<String> roles = List.of("ADMIN", "USER");
        Map<String, Integer> ports = Map.of("http", 80, "https", 443);
    }
}
```

---

## 8. Unmodifiable vs Immutable

| Type | Meaning |
|---|---|
| Unmodifiable collection | wrapper blocks modification through current reference |
| Immutable collection | underlying data itself is fixed and cannot be changed |

### Interview point
`Collections.unmodifiableList(list)` is not the same as deep immutability. If original list changes, wrapper reflects it.

---

## 9. `ConcurrentHashMap` vs Synchronized Map

| Feature | `ConcurrentHashMap` | `Collections.synchronizedMap()` |
|---|---|---|
| Thread safety | yes | yes |
| Scalability | better | lower under contention |
| Null support | no null key/value | follows wrapped map behavior |
| Preferred for heavy concurrency | yes | usually no |

---

## 10. Production Use Cases
- **`ConcurrentHashMap`** -> shared cache, request counters, token store metadata
- **`CopyOnWriteArrayList`** -> listeners, subscribers, config snapshots
- **`BlockingQueue`** -> worker jobs, event processing
- **immutable collections** -> constant configs, permissions, fixed lookup values

---

## 11. Interview Notes
- normal collections are not thread-safe by default
- `ConcurrentHashMap` is preferred over `Hashtable`
- `CopyOnWriteArrayList` is useful only for read-heavy workloads
- immutable collections reduce accidental shared-state bugs
- synchronized wrappers are simple but may not scale well

---

## 12. Quick Revision
| Topic | Best short description |
|---|---|
| synchronized collections | thread-safe wrappers around normal collections |
| `ConcurrentHashMap` | scalable concurrent map |
| `CopyOnWriteArrayList` | safe iteration, costly writes |
| `BlockingQueue` | producer-consumer queue |
| immutable collections | cannot be modified after creation |
| unmodifiable collection | wrapper that blocks mutation through that view |
