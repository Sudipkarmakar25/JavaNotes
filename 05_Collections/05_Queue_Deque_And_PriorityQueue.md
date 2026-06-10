# Queue, Deque and PriorityQueue

## 1. What is `Queue`?
`Queue` is a collection designed for **ordered processing**.

### Common behavior
- usually FIFO: first in, first out
- used when elements are processed in arrival order
- duplicates are generally allowed

### Common backend uses
- task processing
- buffering
- message handling
- breadth-first traversal

---

## 2. Core Queue Methods

| Method | Behavior |
|---|---|
| `offer()` | inserts element |
| `poll()` | removes and returns head, or null |
| `peek()` | returns head without removing, or null |
| `add()` | insert, may throw exception |
| `remove()` | remove head, may throw exception |
| `element()` | read head, may throw exception |

### Interview note
Prefer `offer()` / `poll()` / `peek()` when you want safer API behavior without exceptions for empty/full cases.

---

## 3. `Deque`
`Deque` means **double-ended queue**.

### It supports both
- FIFO queue behavior
- LIFO stack behavior

### Common methods
- `addFirst()`
- `addLast()`
- `pollFirst()`
- `pollLast()`
- `peekFirst()`
- `peekLast()`

### Why important
Modern Java often prefers `Deque` instead of legacy `Stack`.

---

## 4. `ArrayDeque`
`ArrayDeque` is a resizable array-based implementation of `Deque`.

### Characteristics
- fast insert/remove at both ends
- no null elements allowed
- usually preferred over `Stack`
- usually preferred over `LinkedList` when only deque behavior is needed

### Example
```java
import java.util.ArrayDeque;
import java.util.Deque;

class Demo {
    void run() {
        Deque<Integer> dq = new ArrayDeque<>();
        dq.push(10);
        dq.push(20);
        System.out.println(dq.pop());
    }
}
```

### Best use
Stack and queue behavior with low overhead.

---

## 5. `PriorityQueue`
`PriorityQueue` does **not** follow insertion order.

It returns elements based on:
- natural ordering, or
- custom comparator

### Internal working
It is backed by a **heap**.

### Heap basics
Interview-safe summary:
- complete binary tree concept
- top element has highest or lowest priority depending on ordering
- insert/remove maintain heap property

### Characteristics
- duplicates allowed
- null not allowed
- fastest access is for head element only
- not sorted fully during iteration

### Example
```java
import java.util.PriorityQueue;

class Demo {
    void run() {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        pq.offer(30);
        pq.offer(10);
        pq.offer(20);
        System.out.println(pq.poll());
    }
}
```

---

## 6. FIFO vs LIFO vs Priority

| Structure | Processing rule | Typical type |
|---|---|---|
| FIFO | first in, first out | `Queue` |
| LIFO | last in, first out | `Deque` as stack |
| Priority-based | element with priority first | `PriorityQueue` |

---

## 7. `Queue` vs `Deque` vs `PriorityQueue`

| Feature | `Queue` | `Deque` / `ArrayDeque` | `PriorityQueue` |
|---|---|---|---|
| Basic idea | FIFO processing | two-ended processing | priority-based retrieval |
| Stack support | no | yes | no |
| Null support | depends on impl | `ArrayDeque` no | no |
| Order guarantee | queue order | end-based order | priority order only |
| Best use | task sequencing | queue + stack | scheduling/top-priority tasks |

---

## 8. Time Complexity Intuition

| Operation | `ArrayDeque` | `PriorityQueue` |
|---|---|---|
| add | average O(1) at ends | O(log n) |
| poll/remove head | average O(1) | O(log n) |
| peek head | O(1) | O(1) |
| search | O(n) | O(n) |

### Important
For `PriorityQueue`, only the head has guaranteed priority order. Full iteration is not fully sorted.

---

## 9. Production Use Cases
- **`Queue`** -> request buffering, job execution order
- **`ArrayDeque`** -> stack processing, parser logic, BFS
- **`PriorityQueue`** -> scheduler, top-priority jobs, shortest-path style algorithms

---

## 10. Interview Notes
- `Queue` is mainly FIFO.
- `Deque` can act as both queue and stack.
- `ArrayDeque` is usually preferred over legacy `Stack`.
- `PriorityQueue` is heap-based, not fully sorted collection iteration.
- `poll()` removes head; `peek()` only reads head.

---

## 11. Quick Revision
| Type | Best short description |
|---|---|
| `Queue` | FIFO processing structure |
| `Deque` | double-ended queue, also usable as stack |
| `ArrayDeque` | fast modern deque/stack implementation |
| `PriorityQueue` | heap-based priority retrieval structure |
