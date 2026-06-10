# Concurrent Collections

## 1. Problems with normal collections
- java.util.ArrayList, HashMap, etc. are not thread-safe for concurrent mutation.
- Concurrent modification can cause data corruption, lost updates, or ConcurrentModificationException.
- Synchronizing externally (`synchronized(list)`) is error-prone and can hurt concurrency.

## 2. Thread safety in collections
- Two approaches:
  - Coarse-grained locking (synchronized wrapper) — simple but reduces concurrency.
  - Concurrent / lock-free data structures — better throughput for multi-threaded access.

## 3. ConcurrentHashMap
- Thread-safe Map implementation designed for high concurrency.
- Java 8+ uses internal bins and CAS + synchronized for short critical sections.
- Allows concurrent reads and concurrent writes across different segments/bins.
- No locking for simple reads; write contention localized.

Key methods:
- `putIfAbsent(k,v)`, `computeIfAbsent(k, fn)`, `remove(k, v)`, `replace(k, v)` — atomic helpers.

Small example:

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.putIfAbsent("key", 1);
map.computeIfAbsent("count", k -> 0);
```

## 4. Internal idea of ConcurrentHashMap (brief)
- Uses CAS on buckets and synchronized locking on small portions when resizing or heavy contention.
- Reduces global locking by localizing synchronization and using lock-free reads.
- In JDK8: treeify bins to balanced trees under high-collision scenarios (improves performance).

ASCII (simplified):

```
Threads -> [bin0] [bin1] [bin2] [bin3]
            |      |      |      |
         concurrent access localized per bin
```

## 5. CopyOnWriteArrayList
- Snapshot-style list: on mutation it copies entire array.
- Ideal for many-read, few-write scenarios (listeners, caches with rare updates).
- Iterators are safe without locking and never throw ConcurrentModificationException.

Tradeoffs:
- Fast, lock-free reads; expensive writes (array copy).

Example:

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("a");
for (String s : list) { /* safe concurrent iteration */ }
```

## 6. BlockingQueue basics
- Queue designed for producer-consumer patterns with blocking operations.
- Main implementations: ArrayBlockingQueue (bounded), LinkedBlockingQueue (optionally bounded), PriorityBlockingQueue, SynchronousQueue.
- Key methods:
  - `put()` / `take()` — blocking (wait if full/empty).
  - `offer()` / `poll()` — non-blocking with optional timeout.

Example producer-consumer:

```java
BlockingQueue<String> q = new ArrayBlockingQueue<>(100);
q.put("task");
String t = q.take();
```

## 7. ConcurrentLinkedQueue
- Non-blocking, lock-free FIFO queue based on CAS (good for high-throughput).
- Unbounded and weakly consistent iterators (may miss or repeat elements under concurrent updates).

Use-case: many producers and consumers where occasional stale iteration is acceptable.

## 8. Synchronized collections
- Collections.synchronizedList/Map/Set wrap a collection and synchronize all access on a mutex.
- Simple drop-in thread-safety for small-scale use.
- Iteration must be done inside synchronized block to avoid ConcurrentModificationException:

```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
synchronized (syncList) {
  for (String s : syncList) { ... }
}
```

## 9. Fail-fast vs fail-safe iterator
- Fail-fast (e.g., ArrayList iterator): detects concurrent modification and throws ConcurrentModificationException.
- Fail-safe (e.g., CopyOnWriteArrayList iterator): iterate over a snapshot; safe but may not see recent updates.

Compact table:

| Collection | Iterator type | Concurrent updates |
|---|---:|---|
| ArrayList | Fail-fast | Throws CME on structural change |
| CopyOnWriteArrayList | Fail-safe (snapshot) | Safe, sees snapshot only |
| ConcurrentHashMap | Weakly consistent | May reflect some updates, won't throw CME |

## 10. Concurrent collections vs synchronized collections
- Concurrent collections:
  - Designed for high concurrency.
  - Lock-free or fine-grained locks.
  - Better throughput and scalability.
- Synchronized wrappers:
  - Simple, coarse-grained locking.
  - Easier to reason but can become bottleneck.

## 11. Real-world backend examples
- ConcurrentHashMap for caches and memoization.
- BlockingQueue for work queues in thread pools (Executor consumers, producer threads).
- CopyOnWriteArrayList for event listener lists.
- ConcurrentLinkedQueue for async event delivery and task handoff.

## 12. Common mistakes
- Using CopyOnWriteArrayList for write-heavy workloads (very expensive).
- Relying on iteration order or size snapshot guarantees in concurrent collections.
- Wrapping a collection with `Collections.synchronizedX` and iterating without synchronization.
- Choosing the wrong queue (unbounded queues with producers that outpace consumers → OOM).

## 13. Best practices
- Prefer concurrent collections for multi-threaded access rather than external synchronization.
- Choose collection by access pattern: many reads → CopyOnWriteArrayList; many writes → ConcurrentLinkedQueue or ConcurrentHashMap.
- Use bounded BlockingQueue to provide backpressure.
- Always document concurrency expectations and invariants.

## 14. Interview Quick Notes
- ConcurrentHashMap: high-concurrency Map, atomic helpers (putIfAbsent, computeIfAbsent).
- CopyOnWriteArrayList: snapshot on write, ideal for many-read few-write.
- BlockingQueue: use put/take for producer-consumer.
- Fail-fast vs fail-safe: CME vs snapshot/weakly-consistent.

## 15. Summary
- Use the collection that matches your concurrency pattern.
- Concurrent collections provide higher throughput and simpler code than manual synchronization in most cases.
- Understand iterator behavior and memory/copy costs before choosing CopyOnWrite structures.

---

Keep this note for quick revision on Java concurrent collections.

