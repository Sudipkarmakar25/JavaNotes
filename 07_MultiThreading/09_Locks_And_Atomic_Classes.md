# Locks And Atomic Classes

## 1. Problems with `synchronized` keyword
- Simple and correct for many cases but has limitations:
  - Coarse-grained locking may reduce concurrency.
  - No try-with-timeout semantics (can't fail-fast without additional code).
  - Hard to implement complex lock policies (read/write, fairness) with plain synchronized.
  - Possible to accidentally hold lock during blocking I/O.

## 2. Lock interface (java.util.concurrent.locks.Lock)
- Provides more control than `synchronized`:
  - lock()/unlock(), tryLock(), lockInterruptibly().
  - Can implement fair locks, timeouts, and interruptible acquisition.

## 3. ReentrantLock
- Reentrant: same thread can acquire the lock multiple times.
- Supports fairness policy: `new ReentrantLock(true)`.
- Useful when you need tryLock, timeouts, or condition variables (newCondition()).

Small example:

```java
ReentrantLock lock = new ReentrantLock();
if (lock.tryLock()) {
  try {
    // critical section
  } finally { lock.unlock(); }
}
```

## 4. Fair lock
- Fair locks grant access in FIFO order of requests (reduces starvation).
- Slightly lower throughput than unfair locks (default) due to overhead.
- Use when fairness is required (e.g., real-time or important low-priority tasks).

## 5. tryLock()
- Non-blocking attempt to acquire lock; returns boolean.
- `tryLock(timeout, unit)` waits up to timeout, can be interruptible.
- Useful to avoid deadlock by backing off or retrying.

## 6. ReadWriteLock basics
- Separates read and write locks: allows multiple concurrent readers or one writer.
- Main implementation: `ReentrantReadWriteLock`.
- Good for read-heavy data structures where writes are rare.

Usage pattern:

```java
ReadWriteLock rw = new ReentrantReadWriteLock();
Lock r = rw.readLock();
Lock w = rw.writeLock();

r.lock(); try { readData(); } finally { r.unlock(); }
w.lock(); try { writeData(); } finally { w.unlock(); }
```

## 7. Read lock vs write lock
- Read lock: shared, many threads can hold concurrently.
- Write lock: exclusive, only one thread at a time.

Compact table:

| Lock | Concurrency | Use-case |
|---|---:|---|
| Read lock | Multiple readers | Read-heavy access |
| Write lock | Single writer | Mutations / exclusive updates |

## 8. AtomicInteger
- Atomic classes provide lock-free, thread-safe operations using CAS.
- `AtomicInteger` methods: `get()`, `set()`, `incrementAndGet()`, `compareAndSet()`.

Example:

```java
AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet(); // atomic increment
```

## 9. AtomicLong
- Same semantics for long values.
- Useful for counters, statistics, timestamps.

## 10. CAS (Compare And Swap) basics
- Low-level atomic primitive: compare current value with expected, swap if equal.
- Non-blocking; eliminates need for locks in many scenarios.

CAS loop (concept):

```
do {
  int old = value.get();
  int next = compute(old);
 } while (!value.compareAndSet(old, next));
```

Notes:
- CAS can fail under contention (retry loops), leading to spin and CPU usage.
- ABA problem: value changes A→B→A may fool CAS; use versioning or AtomicStampedReference if needed.

## 11. Volatile keyword basics
- `volatile` ensures visibility: writes by one thread are visible to others immediately.
- Guarantees ordering for that variable (prevents reordering of reads/writes to it).
- Does not provide atomicity for compound actions (e.g., `x++` is not atomic).

When to use volatile:
- Flags (stop request), single-writer multiple-reader scenarios, or to publish immutable objects safely.

## 12. Visibility problem
- Without synchronization or volatile, one thread's update might not be visible to another thread due to caches and compiler optimizations.
- Fix with `synchronized`, `volatile`, or atomic classes.

## 13. synchronized vs Lock

| Aspect | synchronized | Lock (ReentrantLock) |
|---|---:|---|
| Syntax | Built-in keyword | API (lock/unlock) |
| try-with-timeout | No | Yes (tryLock(timeout)) |
| Interruptible lock acquisition | No | Yes (lockInterruptibly) |
| Condition variables | Implicit (wait/notify) | Explicit (newCondition()) |

Use `synchronized` for simple cases; prefer `Lock` for advanced control.

## 14. synchronized vs AtomicInteger
- `synchronized` protects arbitrary critical sections and multiple variables.
- `AtomicInteger` provides lock-free atomic operations for a single variable.

Compact comparison:

| Use-case | synchronized | AtomicInteger |
|---|---:|---|
| Update multiple fields atomically | ✅ | ❌ |
| High-frequency single-variable updates | ❌ (heavy) | ✅ (fast, lock-free) |

## 15. Atomic operations
- Examples: incrementAndGet(), getAndIncrement(), addAndGet(), compareAndSet().
- Typically implemented with CPU CAS instructions via Unsafe or VarHandle in newer JDKs.

## 16. Real-world examples
- Atomic counters for metrics (requests, errors).
- Sequence generators using AtomicLong.
- Read-heavy caches using ReentrantReadWriteLock.
- Short critical sections with ReentrantLock and tryLock to avoid deadlocks.

## 17. Common mistakes
- Using volatile instead of proper synchronization for compound actions.
- Overusing locks leading to contention and reduced throughput.
- Forgetting to unlock in finally blocks.
- Using atomic classes for complex multi-variable invariants (should use locks or transactions).

## 18. Best practices
- Prefer atomic classes for single-variable counters and flags.
- Use `synchronized` for simple, coarse-grained protection.
- Use `ReentrantLock` when you need tryLock, timeouts, conditions, or interruptible waits.
- Keep locked sections minimal and avoid blocking calls inside locks.
- Always release locks in finally blocks or use try-with-resources style helpers.

## 19. Interview Quick Notes
- `ReentrantLock` gives more control than `synchronized` (tryLock, fairness, conditions).
- Atomic classes use CAS and are lock-free for single variables.
- `volatile` ensures visibility but not atomicity.
- ReadWriteLock improves concurrency for read-heavy workloads.

## 20. Summary
- Locks and atomic classes are complementary tools: locks for complex invariants, atomics for fast single-variable operations.
- Choose the smallest, simplest tool that safely solves the problem.
- Understand CAS, visibility, and proper lock usage for interview questions.

---

Keep this note for quick revision on locks and atomic classes.

