# Collections Framework and Hierarchy

## 1. What is the Collections Framework?
The **Java Collections Framework (JCF)** is a unified API for storing, retrieving, and manipulating groups of objects.

It gives:
- standard interfaces
- reusable implementations
- common algorithms
- predictable APIs across data structures

### Why it matters in backend systems
Collections are everywhere in Java backend code:
- request data handling
- caching
- batching
- deduplication
- sorting and filtering
- mapping IDs to objects

---

## 2. Why the Framework Exists
Without a common framework, every team would use different APIs for lists, sets, queues, and maps.

The framework improves:
- **maintainability** -> same API style across structures
- **reusability** -> same code works with many implementations
- **readability** -> developers already know common methods
- **interoperability** -> easy conversion between collection types

---

## 3. `Collection` vs `Collections`

| Term | Type | Meaning |
|---|---|---|
| `Collection` | interface | root interface for `List`, `Set`, `Queue` |
| `Collections` | utility class | helper methods like `sort()`, `reverse()`, `binarySearch()` |

### Example
```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;

class Demo {
    void run() {
        Collection<String> names = new ArrayList<>();
        names.add("A");
        names.add("B");

        ArrayList<String> list = new ArrayList<>(names);
        Collections.reverse(list);
    }
}
```

### Interview point
`Map` is part of the collections framework, but it does **not** extend `Collection`.

---

## 4. `Iterable` Interface
`Iterable` is the root contract for objects that can be iterated.

### Main method
`Iterator<T> iterator()`

### Why important
It enables:
- enhanced for-loop
- common iteration style
- stream creation support in many cases

### Example
```java
import java.util.List;

class Demo {
    void print(List<String> list) {
        for (String value : list) {
            System.out.println(value);
        }
    }
}
```

---

## 5. Core Hierarchy
```text
Iterable
 └── Collection
      ├── List
      │    ├── ArrayList
      │    ├── LinkedList
      │    ├── Vector
      │    └── Stack
      ├── Set
      │    ├── HashSet
      │    ├── LinkedHashSet
      │    └── TreeSet
      └── Queue
           ├── Deque
           │    ├── ArrayDeque
           │    └── LinkedList
           └── PriorityQueue

Map
 ├── HashMap
 ├── LinkedHashMap
 ├── TreeMap
 ├── Hashtable
 └── ConcurrentHashMap
```

---

## 6. Interfaces Overview

| Interface | Core idea | Duplicates | Ordering |
|---|---|---|---|
| `List` | ordered, index-based collection | allowed | insertion order |
| `Set` | unique elements | not allowed | depends on implementation |
| `Queue` | processing order structure | allowed | FIFO / priority |
| `Deque` | double-ended queue | allowed | FIFO or LIFO |
| `Map` | key-value pairs | duplicate keys not allowed | depends on implementation |

### Quick intuition
- use **List** when order and indexing matter
- use **Set** when uniqueness matters
- use **Queue/Deque** when processing order matters
- use **Map** when lookup by key matters

---

## 7. Common Base Methods
Methods from `Collection` commonly asked in interviews:
- `add()`
- `remove()`
- `contains()`
- `size()`
- `isEmpty()`
- `clear()`
- `iterator()`

These methods exist across most collection types, but actual performance depends on implementation.

---

## 8. Time Complexity Basics
Complexity depends on internal structure.

| Operation | Array-backed | Linked structure | Hash-based | Tree-based |
|---|---|---|---|---|
| add | often fast | often fast | average fast | log n |
| search | linear | linear | average fast | log n |
| remove | may shift elements | good after node access | average fast | log n |
| ordered traversal | yes | yes | not guaranteed | sorted order |

### Important interview intuition
- **ArrayList** -> good random access
- **LinkedList** -> sequential structure, poor random access
- **HashSet/HashMap** -> fast average lookup
- **TreeSet/TreeMap** -> sorted but slower than hash structures

---

## 9. Selection Rules
```text
Need duplicates + order? -> List
Need uniqueness? -> Set
Need key-value lookup? -> Map
Need FIFO/LIFO processing? -> Queue / Deque
Need sorted data? -> TreeSet / TreeMap
Need fast average lookup? -> HashSet / HashMap
```

---

## 10. Interview Notes
- Collections Framework = interfaces + implementations + algorithms.
- `Collection` is an interface; `Collections` is a utility class.
- `Iterable` enables enhanced for-loop.
- `Map` is part of the framework but outside the `Collection` hierarchy.
- Always choose collection based on access pattern, ordering, uniqueness, and concurrency needs.

---

## 11. Quick Revision
| Topic | One-line takeaway |
|---|---|
| Collections Framework | standard API for data structures |
| `Iterable` | root iteration contract |
| `Collection` | base interface for `List`, `Set`, `Queue` |
| `Collections` | utility/helper class |
| `Map` | separate key-value hierarchy |
| complexity choice | internal structure decides performance |
