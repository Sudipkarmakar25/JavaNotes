# List Interface and Implementations

## 1. What is `List`?
`List` is an ordered collection that:
- preserves insertion order
- allows duplicates
- allows positional/index-based access
- usually allows nulls depending on implementation

### Common use cases
- ordered API response data
- batch item processing
- UI table rows
- request payload collections

---

## 2. Common `List` Methods

| Method | Purpose |
|---|---|
| `add()` | insert element |
| `get(index)` | read by index |
| `set(index, value)` | replace element |
| `remove()` | remove by index or value |
| `contains()` | search element |
| `size()` | element count |
| `subList()` | partial view |

---

## 3. `ArrayList`
### Internal working
`ArrayList` is backed by a **resizable array**.

### Important points
- very fast random access
- append is usually fast
- middle insertion/removal may shift elements
- not synchronized

### Resizing
When array becomes full, a larger array is created and elements are copied.

Interview-safe simplification:
- old capacity full
- new bigger array allocated
- old data copied

### Example
```java
import java.util.ArrayList;
import java.util.List;

class Demo {
    void run() {
        List<String> names = new ArrayList<>();
        names.add("A");
        names.add("B");
        System.out.println(names.get(1));
    }
}
```

### Best fit
Read-heavy code and index-based access.

---

## 4. `LinkedList`
### Internal working
`LinkedList` is implemented as a **doubly linked list**.

Each node stores:
- data
- previous node reference
- next node reference

### Important points
- sequential traversal
- insertion/removal at ends is efficient
- random access is slow
- higher memory overhead than `ArrayList`
- also implements `Deque`

### Example
```java
import java.util.LinkedList;

class Demo {
    void run() {
        LinkedList<Integer> list = new LinkedList<>();
        list.addFirst(10);
        list.addLast(20);
    }
}
```

### Best fit
Queue/deque style usage or frequent end operations.

---

## 5. `Vector`
`Vector` is a legacy dynamic array implementation.

### Important points
- similar to `ArrayList`
- synchronized
- slower in normal single-threaded use
- rarely preferred in modern code

### Interview note
Prefer `ArrayList` in normal code and specialized concurrent structures in multithreaded code.

---

## 6. `Stack`
`Stack` is a legacy LIFO structure that extends `Vector`.

### Common methods
- `push()`
- `pop()`
- `peek()`

### Interview note
In modern Java, `Deque` / `ArrayDeque` is usually preferred over `Stack`.

---

## 7. `ArrayList` vs `LinkedList` vs `Vector` vs `Stack`

| Feature | `ArrayList` | `LinkedList` | `Vector` | `Stack` |
|---|---|---|---|---|
| Internal structure | dynamic array | doubly linked list | dynamic array | legacy stack on `Vector` |
| Order preserved | yes | yes | yes | yes |
| Duplicates | yes | yes | yes | yes |
| Random access | fast | slow | fast | not main use |
| Middle insert/remove | costly | better after traversal | costly | not main use |
| Synchronization | no | no | yes | yes |
| Preferred today | yes | situational | rarely | rarely |

---

## 8. Time Complexity

| Operation | `ArrayList` | `LinkedList` | `Vector` |
|---|---|---|---|
| add at end | average O(1) | O(1) | average O(1) |
| get by index | O(1) | O(n) | O(1) |
| search | O(n) | O(n) | O(n) |
| insert/remove middle | O(n) | O(n) traversal, then link update | O(n) |
| add/remove first | O(n) | O(1) | O(n) |

### Important
Complexity alone is not enough. Memory cost, locality, traversal pattern, and concurrency also matter.

---

## 9. Production Use Cases
- **`ArrayList`** -> API DTO lists, query results, batch records
- **`LinkedList`** -> queue-like processing, deque operations
- **`Vector`** -> mostly legacy code only
- **`Stack`** -> legacy LIFO code; prefer `ArrayDeque`

---

## 10. Interview Notes
- `ArrayList` is the default `List` choice in most business code.
- `LinkedList` is not automatically faster than `ArrayList`.
- `Vector` is synchronized legacy; modern code rarely uses it.
- `Stack` is legacy; prefer `Deque` for stack behavior.
- `ArrayList` resizing causes copy cost, but append is still usually efficient overall.

---

## 11. Quick Revision
| Type | Best short description |
|---|---|
| `ArrayList` | dynamic array, best general-purpose list |
| `LinkedList` | doubly linked list, better for end operations |
| `Vector` | synchronized legacy `ArrayList`-like class |
| `Stack` | legacy LIFO class, usually replaced by `Deque` |
