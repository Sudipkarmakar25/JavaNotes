# Iterators, Comparable, Comparator and Collections Utility

## 1. Traversal APIs Overview
Traversal APIs let you move through collection elements safely and consistently.

Main ones asked in interviews:
- `Iterator`
- `ListIterator`
- `Enumeration`

---

## 2. `Iterator`
`Iterator` is the standard forward-only traversal API.

### Common methods
- `hasNext()`
- `next()`
- `remove()`

### Example
```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

class Demo {
    void run() {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");

        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

### Use case
General traversal when forward iteration is enough.

---

## 3. `ListIterator`
`ListIterator` is available only for `List` implementations.

### Extra abilities over `Iterator`
- traverse forward and backward
- update element with `set()`
- add during iteration
- get previous element

### Common methods
- `hasPrevious()`
- `previous()`
- `set()`
- `add()`

### Best fit
When you need bidirectional traversal or modification while iterating a list.

---

## 4. `Enumeration`
`Enumeration` is a legacy traversal API.

### Common methods
- `hasMoreElements()`
- `nextElement()`

### Interview point
It is older than `Iterator` and provides fewer operations. Mostly seen in legacy APIs like older `Vector` usage.

---

## 5. Fail-fast vs Fail-safe

| Type | Meaning |
|---|---|
| Fail-fast | detects structural modification during iteration and may throw `ConcurrentModificationException` |
| Fail-safe | iterates over safe snapshot/copy or concurrent structure behavior |

### Fail-fast example
Typical collections like `ArrayList` and `HashMap` iterators are fail-fast.

### Fail-safe style examples
- `CopyOnWriteArrayList`
- some concurrent collection iterators

### Interview note
Fail-fast is best-effort detection, not a formal thread-safety guarantee.

---

## 6. `Comparable`
`Comparable` defines **natural ordering** inside the class itself.

### Main method
```java
int compareTo(T other)
```

### Example
```java
class Product implements Comparable<Product> {
    int price;

    Product(int price) {
        this.price = price;
    }

    @Override
    public int compareTo(Product other) {
        return Integer.compare(this.price, other.price);
    }
}
```

### Use case
Default sorting rule for domain objects.

---

## 7. `Comparator`
`Comparator` defines **external/custom ordering**.

### Main method
```java
int compare(T a, T b)
```

### Example
```java
import java.util.Comparator;

class ByLength implements Comparator<String> {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
}
```

### Why useful
One class can have multiple sorting strategies.

---

## 8. `Comparable` vs `Comparator`

| Aspect | `Comparable` | `Comparator` |
|---|---|---|
| Location of logic | inside class | outside class |
| Method | `compareTo()` | `compare()` |
| Sort rule count | usually one natural order | many custom orders possible |
| Best use | default ordering | flexible external ordering |

---

## 9. Custom Sorting and Lambda Sorting
### Using comparator with lambda
```java
import java.util.ArrayList;
import java.util.List;

class Demo {
    void run() {
        List<String> names = new ArrayList<>();
        names.add("Java");
        names.add("C");
        names.sort((a, b) -> Integer.compare(a.length(), b.length()));
    }
}
```

### Interview point
Modern Java sorting often uses lambdas instead of separate comparator classes.

---

## 10. `Collections` Utility Class
`Collections` is a utility class with helper algorithms for collections.

### Common methods
- `sort()`
- `binarySearch()`
- `reverse()`
- `shuffle()`
- `max()`
- `min()`
- `frequency()`

### Example
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Demo {
    void run() {
        List<Integer> list = new ArrayList<>();
        list.add(3);
        list.add(1);
        list.add(2);
        Collections.sort(list);
        Collections.reverse(list);
    }
}
```

---

## 11. Important Utility Methods

| Method | Purpose |
|---|---|
| `sort()` | sort list |
| `binarySearch()` | fast search in sorted list |
| `reverse()` | reverse order |
| `shuffle()` | randomize order |

### Important
`binarySearch()` expects data to be sorted using compatible ordering.

---

## 12. Interview Notes
- `Iterator` is forward-only and most commonly used.
- `ListIterator` is for lists and supports backward traversal.
- `Enumeration` is legacy.
- `Comparable` = natural order; `Comparator` = custom order.
- `Collections` is utility class, not collection hierarchy.
- Fail-fast does not mean thread-safe.

---

## 13. Quick Revision
| Topic | Best short description |
|---|---|
| `Iterator` | forward traversal API |
| `ListIterator` | bidirectional list traversal |
| `Enumeration` | legacy traversal API |
| fail-fast | detects concurrent structural modification |
| `Comparable` | default/natural order |
| `Comparator` | custom external order |
| `Collections` | utility algorithms for collections |
