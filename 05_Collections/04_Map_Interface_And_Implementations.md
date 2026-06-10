# Map Interface and Implementations

## 1. What is `Map`?
`Map` stores data as **key-value pairs**.

### Core rules
- keys are unique
- values may be duplicated
- lookup is usually done by key
- `Map` is part of the collections framework but not a child of `Collection`

### Common backend uses
- ID to object mapping
- caching
- configuration storage
- request headers / query parameters
- aggregation counters

---

## 2. Map Hierarchy Overview
```text
Map
 ├── HashMap
 ├── LinkedHashMap
 ├── TreeMap
 ├── Hashtable
 └── ConcurrentHashMap
```

---

## 3. Common `Map` Methods

| Method | Purpose |
|---|---|
| `put(k, v)` | insert/update key-value |
| `get(k)` | read value by key |
| `remove(k)` | remove entry |
| `containsKey(k)` | key lookup |
| `containsValue(v)` | value lookup |
| `keySet()` | all keys |
| `values()` | all values |
| `entrySet()` | key-value entry view |

### Interview point
`entrySet()` iteration is usually preferred when both key and value are needed.

---

## 4. `HashMap`
### Internal working
`HashMap` is the most important `Map` implementation for interviews.

It uses:
- an array of buckets
- hash-based bucket selection
- collision handling inside buckets

### Important concepts
- **bucket** -> slot where entries are grouped
- **collision** -> different keys land in same bucket
- **load factor** -> threshold controlling resize timing
- **rehashing** -> entries redistributed into larger bucket array
- **treeification** -> heavily collided bucket may become tree-based in modern JDKs

### Characteristics
- average fast lookup
- no ordering guarantee
- allows one null key and multiple null values
- not synchronized

### Example
```java
import java.util.HashMap;
import java.util.Map;

class Demo {
    void run() {
        Map<Integer, String> users = new HashMap<>();
        users.put(1, "Amit");
        users.put(2, "Rahul");
        System.out.println(users.get(1));
    }
}
```

---

## 5. Hashing, Collisions, Load Factor, Rehashing
### Hashing
Key hash decides probable bucket location.

### Collision handling
If multiple keys map to same bucket, they are stored together using internal bucket structures.

### Load factor
Default interview-safe idea:
- more entries compared to bucket size
- when threshold is crossed, resize happens

### Rehashing
Resize creates larger internal storage and redistributes entries.

### Treeification
If one bucket gets too many collisions, modern `HashMap` may use tree structure there for better worst-case performance.

---

## 6. `equals()` and `hashCode()`
For map keys, these methods are critical.

### Why?
- `hashCode()` helps find bucket
- `equals()` confirms actual key equality

### If implemented badly
- duplicate-looking keys may behave incorrectly
- lookup may fail
- remove/contains may behave unexpectedly

### Interview-safe rule
If two objects are equal according to `equals()`, they must return the same `hashCode()`.

---

## 7. `LinkedHashMap`
`LinkedHashMap` maintains predictable iteration order.

### Order types
- insertion order by default
- access order optionally, useful in caching patterns

### Characteristics
- keeps order
- slightly more overhead than `HashMap`
- allows null key and null values

### Production use case
LRU-style cache logic often uses access-ordered `LinkedHashMap`.

---

## 8. `TreeMap`
`TreeMap` stores keys in sorted order.

### Internal working
It uses a **Red-Black Tree**.

### Characteristics
- sorted by natural order or comparator
- null keys not allowed in normal use
- operations are slower than `HashMap` average lookup
- useful when sorted traversal is needed

### Best use
Sorted reports, leaderboard keys, range-based operations.

---

## 9. `Hashtable`
`Hashtable` is a legacy synchronized map.

### Characteristics
- synchronized
- no null key
- no null values
- usually replaced in modern code

### Interview point
Prefer `ConcurrentHashMap` for concurrent use instead of `Hashtable`.

---

## 10. `ConcurrentHashMap`
`ConcurrentHashMap` is designed for concurrent access.

### Characteristics
- thread-safe
- better scalability than `Hashtable`
- null keys and values not allowed
- optimized for concurrent reads and updates

### Production use case
Shared caches, counters, request/session metadata in concurrent systems.

---

## 11. Comparison Table

| Feature | `HashMap` | `LinkedHashMap` | `TreeMap` | `Hashtable` | `ConcurrentHashMap` |
|---|---|---|---|---|---|
| Ordering | none | insertion/access | sorted keys | none | none |
| Null key | yes | yes | no | no | no |
| Null values | yes | yes | yes* | no | no |
| Thread-safe | no | no | no | yes | yes |
| Internal base | buckets | buckets + links | Red-Black Tree | legacy hash table | concurrent hash structure |
| Main use | general key lookup | ordered map / cache | sorted keys | legacy code | concurrent lookup |

`*` `TreeMap` allows null values, but not null keys in normal comparable/comparator usage.

---

## 12. Time Complexity

| Operation | `HashMap` | `LinkedHashMap` | `TreeMap` | `ConcurrentHashMap` |
|---|---|---|---|---|
| put | average O(1) | average O(1) | O(log n) | average O(1) |
| get | average O(1) | average O(1) | O(log n) | average O(1) |
| remove | average O(1) | average O(1) | O(log n) | average O(1) |
| ordered iteration | no | yes | sorted | no |

---

## 13. Production Selection Rules
```text
Need fastest average key lookup? -> HashMap
Need order + lookup? -> LinkedHashMap
Need sorted keys? -> TreeMap
Need thread-safe concurrent access? -> ConcurrentHashMap
Only dealing with legacy synchronized code? -> Hashtable
```

---

## 14. Interview Notes
- `HashMap` is the most frequently asked collection in Java interviews.
- Buckets, collisions, load factor, rehashing, and `equals()`/`hashCode()` are must-know topics.
- `LinkedHashMap` is important for ordered iteration and LRU-style caching.
- `TreeMap` trades speed for sorted order.
- `ConcurrentHashMap` is the practical concurrent map choice.

---

## 15. Quick Revision
| Type | Best short description |
|---|---|
| `HashMap` | fast average unordered key-value store |
| `LinkedHashMap` | ordered `HashMap` |
| `TreeMap` | sorted key-value map |
| `Hashtable` | legacy synchronized map |
| `ConcurrentHashMap` | modern concurrent map |
