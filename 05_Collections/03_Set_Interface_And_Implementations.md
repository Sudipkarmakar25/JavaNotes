# Set Interface and Implementations

## 1. What is `Set`?
`Set` is a collection that stores **unique elements**.

### Core rules
- duplicates are not allowed
- order depends on implementation
- uniqueness logic depends on equality mechanism

### Where uniqueness comes from
- `HashSet` / `LinkedHashSet` -> `equals()` and `hashCode()`
- `TreeSet` -> ordering via `Comparable` / `Comparator`

---

## 2. Common `Set` Methods
- `add()`
- `remove()`
- `contains()`
- `size()`
- `isEmpty()`
- `iterator()`

### Important
`add()` returns:
- `true` if element was inserted
- `false` if duplicate already existed

---

## 3. `HashSet`
### Internal working
`HashSet` is backed internally by a `HashMap`.

Conceptually:
- set element becomes map key
- a dummy constant value is stored internally

### Characteristics
- no guaranteed order
- one null allowed
- fast average add/search/remove
- depends on good `hashCode()` and `equals()`

### Example
```text
Set<String> set = new HashSet<>();
set.add("A")        -> true
set.add("A")        -> false
```

### Best use
Fast uniqueness checks and deduplication.

---

## 4. `LinkedHashSet`
`LinkedHashSet` extends `HashSet` behavior by maintaining **insertion order**.

### Characteristics
- unique elements
- preserves insertion order
- one null allowed
- slightly more overhead than `HashSet`

### Best use
When you need both:
- uniqueness
- predictable iteration order

---

## 5. `TreeSet`
`TreeSet` stores elements in **sorted order**.

### Internal working
`TreeSet` is backed by a `TreeMap`, which uses a **Red-Black Tree**.

### Characteristics
- sorted order
- no null in normal use
- slower than hash-based sets for average operations
- uses natural ordering or custom comparator

### Example
```java
import java.util.Set;
import java.util.TreeSet;

class Demo {
    void run() {
        Set<Integer> set = new TreeSet<>();
        set.add(30);
        set.add(10);
        set.add(20);
        System.out.println(set);
    }
}
```

### Red-Black Tree basics
Interview-safe view:
- self-balancing binary search tree
- keeps height near logarithmic
- gives sorted traversal with log n operations

---

## 6. Ordering and Null Handling

| Implementation | Ordering | Null handling |
|---|---|---|
| `HashSet` | no guaranteed order | one null allowed |
| `LinkedHashSet` | insertion order | one null allowed |
| `TreeSet` | sorted order | usually null not allowed |

### Interview point
Order is one of the biggest reasons to choose between set implementations.

---

## 7. Hashing Basics
For hash-based sets:
1. object hash is computed
2. bucket location is determined
3. equality check verifies duplicate or not

### Why `equals()` and `hashCode()` matter
If they are not implemented correctly:
- duplicates may appear logically
- lookup may behave unexpectedly
- set behavior becomes incorrect

---

## 8. `HashSet` vs `LinkedHashSet` vs `TreeSet`

| Feature | `HashSet` | `LinkedHashSet` | `TreeSet` |
|---|---|---|---|
| Uniqueness | yes | yes | yes |
| Ordering | no guarantee | insertion order | sorted order |
| Null support | yes | yes | usually no |
| Internal structure | hash table | hash table + linked order | Red-Black Tree |
| Average lookup | fast | fast | slower |
| Best use | fast deduplication | dedup + stable order | sorted unique data |

---

## 9. Time Complexity

| Operation | `HashSet` | `LinkedHashSet` | `TreeSet` |
|---|---|---|---|
| add | average O(1) | average O(1) | O(log n) |
| contains | average O(1) | average O(1) | O(log n) |
| remove | average O(1) | average O(1) | O(log n) |
| traversal | unordered | insertion order | sorted order |

---

## 10. Production Use Cases
- **`HashSet`** -> remove duplicates from IDs, emails, tags
- **`LinkedHashSet`** -> unique values while preserving client input order
- **`TreeSet`** -> sorted rankings, ordered codes, unique sorted values

---

## 11. Interview Notes
- `Set` means uniqueness, not sorting.
- `HashSet` is most common for fast uniqueness checks.
- `LinkedHashSet` adds stable insertion order.
- `TreeSet` is sorted and comparator-driven.
- For `HashSet`, correct `equals()` and `hashCode()` are critical.

---

## 12. Quick Revision
| Type | Best short description |
|---|---|
| `HashSet` | fast unordered unique set |
| `LinkedHashSet` | unique set with insertion order |
| `TreeSet` | sorted unique set |
