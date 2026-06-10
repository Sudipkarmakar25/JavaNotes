# Collections Interview Questions and Real Usage

## 1. Which Collection to Use When?

| Need | Best common choice |
|---|---|
| ordered duplicate data | `ArrayList` |
| unique values | `HashSet` |
| unique + insertion order | `LinkedHashSet` |
| sorted unique values | `TreeSet` |
| key-value lookup | `HashMap` |
| ordered key-value lookup | `LinkedHashMap` |
| sorted key lookup | `TreeMap` |
| concurrent key-value access | `ConcurrentHashMap` |
| FIFO processing | `Queue` |
| stack behavior | `ArrayDeque` |
| priority processing | `PriorityQueue` |

---

## 2. Backend Production Examples
### Caching
- `HashMap` for local non-concurrent cache
- `ConcurrentHashMap` for shared concurrent in-memory metadata
- `LinkedHashMap` for simple LRU-style access-order cache

### Logging / event buffering
- `Queue` / `Deque` for in-order event processing
- `BlockingQueue` in background worker pipelines

### Database result handling
- `List` for ordered query result rows
- `Map<ID, Entity>` for fast lookup after fetch
- `Set` for deduplicating IDs or tags

### API / service layer
- `List` for response DTO collections
- `Map<String, Object>` for dynamic metadata or headers
- `Set<String>` for permissions / roles

---

## 3. Scenario-Based Selection
### Scenario 1: Need fastest average lookup by key
Use `HashMap`.

### Scenario 2: Need same as `HashMap` but thread-safe
Use `ConcurrentHashMap`.

### Scenario 3: Need sorted keys
Use `TreeMap`.

### Scenario 4: Need unique values only
Use `HashSet`.

### Scenario 5: Need unique values in insertion order
Use `LinkedHashSet`.

### Scenario 6: Need stack behavior
Use `ArrayDeque`, not legacy `Stack`.

### Scenario 7: Need processing by priority
Use `PriorityQueue`.

---

## 4. Performance Discussion
### Read-heavy vs write-heavy
- `ArrayList` is good for read-heavy indexed access
- `CopyOnWriteArrayList` only makes sense for read-heavy concurrent scenarios
- `LinkedList` is not generally better unless access pattern truly fits it

### Ordering cost
- hash-based collections are usually faster for average lookup
- tree-based collections cost more because they maintain sorted order

### Synchronization cost
- synchronized wrappers are simpler but may scale poorly
- concurrent collections are better for highly parallel systems

### Memory tradeoff
- linked structures usually consume more memory than arrays
- ordered structures like `LinkedHashMap` add extra overhead for links

---

## 5. Common Interview Questions
### Why is `HashMap` fast?
Because it uses hashing and bucket-based lookup, giving average O(1) put/get/remove.

### Why does `Set` not allow duplicates?
Because set implementations enforce uniqueness using equality rules (`equals()` / `hashCode()` or comparator logic).

### When would you use `TreeMap` instead of `HashMap`?
When sorted keys, range queries, or ordered traversal matter more than raw average lookup speed.

### Why prefer `ConcurrentHashMap` over `Hashtable`?
Because it is modern, more scalable, and designed for better concurrent performance.

### Why is `LinkedList` rarely the default choice?
Because random access is slow, memory overhead is higher, and `ArrayList` fits most business workloads better.

### What is the difference between fail-fast and fail-safe?
Fail-fast detects structural modification during iteration; fail-safe style iteration works on safe snapshot/concurrent behavior.

---

## 6. Real Interview Answer Style
When asked "Which collection would you choose?", answer using this format:

```text
1. state the requirement
2. pick the collection
3. justify by ordering / uniqueness / lookup / concurrency / complexity
4. mention tradeoff
```

### Example
**Question:** Which map for concurrent request counters?

**Answer:** I would use `ConcurrentHashMap` because multiple threads may update counters concurrently. It is thread-safe and scales better than `Hashtable`. If ordering were required, I would evaluate a different design because ordering adds extra cost.

---

## 7. Common Mistakes
- choosing `LinkedList` without real need
- using `Hashtable` in modern code
- assuming `HashMap` iteration order is fixed
- forgetting `equals()` and `hashCode()` for keys/set elements
- using `PriorityQueue` expecting full sorted iteration
- using synchronized wrappers when high concurrency needs specialized collections

---

## 8. Best Practices
- choose collection by access pattern, not habit
- prefer `ArrayList` as default list unless another structure clearly fits better
- prefer `HashMap` / `HashSet` for fast average lookup
- use tree-based collections only when sorted order matters
- use concurrent collections in multithreaded code
- prefer `ArrayDeque` over legacy `Stack`
- measure performance for critical paths instead of guessing

---

## 9. Quick Revision Decision Map
```text
Need indexed ordered data? -> ArrayList
Need uniqueness? -> HashSet
Need uniqueness + order? -> LinkedHashSet
Need sorted values/keys? -> TreeSet / TreeMap
Need key lookup? -> HashMap
Need concurrent key lookup? -> ConcurrentHashMap
Need FIFO? -> Queue
Need LIFO? -> ArrayDeque
Need priority order? -> PriorityQueue
```

---

## 10. Final Interview Notes
- No collection is best everywhere.
- Best choice depends on order, uniqueness, lookup pattern, update pattern, memory, and concurrency.
- In interviews, always justify the choice with tradeoffs, not just the name of the class.
