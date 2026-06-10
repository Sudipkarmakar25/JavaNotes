# Synchronization And Race Condition

## 1. Introduction
- Why synchronization is needed:
  - Protect shared resources from concurrent access.
  - Ensure correct results and predictable behavior.
- Shared resources: heap objects, static fields, files, DB connections.

## 2. What is Race Condition
- Definition: two or more threads access and modify shared data concurrently and outcome depends on timing.
- Real-world analogy: two people writing on the same whiteboard at the same time.
- Incorrect output example: lost updates in counters.

Race condition flow (ASCII):

```
Thread-A read X -> Thread-B read X -> A writes X+1 -> B writes X+1  (one update lost)
```

## 3. Shared Mutable State
- Shared objects on heap are visible to multiple threads.
- Mutable state causes problems without coordination.
- Immutable objects are safe to share.

## 4. Critical Section
- Definition: code region that accesses shared mutable state and must not be run by multiple threads concurrently.
- Goal: allow only one thread inside critical section for correctness.

## 5. Thread Safety
- Thread-safe: code works correctly when accessed by multiple threads.
- Strategies: immutability, synchronization, concurrent data structures, atomic primitives.

## 6. synchronized Keyword
- Purpose: provide mutual exclusion and memory visibility via monitor lock.
- Internal: each Java object has a monitor; synchronized acquires/releases the monitor.
- Only one thread can hold an object's monitor at a time.

## 7. Synchronized Method
- Syntax: `public synchronized void m() { ... }`
- Acquires the object's monitor (this) for instance methods.
- Example:

```java
class Counter {
  private int c = 0;
  public synchronized void inc() { c++; }
  public synchronized int get() { return c; }
}
```

## 8. Synchronized Block
- Syntax: `synchronized(lock) { ... }`
- Prefer when you need fine-grained locking or to limit scope.
- Example:

```java
class Counter2 {
  private int c = 0; private final Object lock = new Object();
  public void inc() { synchronized(lock) { c++; } }
}
```

## 9. Object Lock vs Class Lock
- Object lock: acquired on a specific instance (`synchronized(this)` or instance method).
- Class lock: acquired on Class object (`synchronized(MyClass.class)` or static synchronized methods).

Comparison:

| Aspect | Object Lock | Class Lock |
|---|---:|---:|
| Scope | per-instance | single per class |
| Use case | protect instance state | protect static/shared state |

## 10. Static Synchronization
- `public static synchronized void m() {}` uses class-level lock.
- Useful to guard static fields or class-wide invariants.

## 11. Problems Without Synchronization
- Counter example (lost update): multiple threads incrementing `int` without sync.
- Bank account example: withdraw/read-modify-write leads to negative balance.
- Data inconsistency and unpredictable behavior.

Quick unsynchronized counter (bad):

```java
class BadCounter { int c = 0; void inc() { c++; } }
```

## 12. Deadlock (brief pointer)
- Deadlock is a related topic (circular wait for locks).
- Details and prevention strategies are covered in `06_Deadlock_Livelock_Starvation.md`.

## 13. volatile (brief)
- `volatile` ensures visibility of writes to a single variable across threads.
- Does not provide atomicity for compound actions (e.g., ++).
- See `09_Locks_And_Atomic_Classes.md` for detailed atomic/volatile discussion and CAS.

## 14. synchronized vs volatile

| Feature | synchronized | volatile |
|---|---:|---:|
| Mutual exclusion | Yes | No |
| Visibility | Yes | Yes |
| Atomicity | Can (via critical section) | No |
| Use case | Protect complex state | Simple flag/visibility |

## 15. Performance Impact of Synchronization
- Blocking threads may wait, reducing throughput.
- Excessive synchronization increases contention and context switches.
- Modern JVMs optimize uncontended synchronized paths (biased locking, etc.) but still cost when contended.

- Minimize synchronized scope; prefer synchronized blocks over whole methods when possible.
- Prefer immutable objects or thread-safe collections (ConcurrentHashMap).
- Use java.util.concurrent utilities (Locks, AtomicInteger, CountDownLatch, Executors).
- Prefer tryLock/timeouts to avoid permanent blocking.
## 16. Best Practices
- Minimize synchronized scope; prefer synchronized blocks over whole methods when possible.
- Prefer immutable objects or thread-safe collections (ConcurrentHashMap).
- Use java.util.concurrent utilities (Locks, AtomicInteger, CountDownLatch, Executors).
- Prefer tryLock/timeouts to avoid permanent blocking.
- Minimize synchronized scope; prefer synchronized blocks over whole methods when possible.
- Prefer immutable objects or thread-safe collections (ConcurrentHashMap).
- Use java.util.concurrent utilities (Locks, AtomicInteger, CountDownLatch, Executors).
- Prefer tryLock/timeouts to avoid permanent blocking.

## 17. Common Mistakes
- Synchronizing on mutable or publicly accessible objects (use private final locks).
- Relying on volatile for compound operations (use Atomic types or synchronization).
- Holding locks while performing I/O or long operations.

## 18. Interview Quick Notes
- Race condition: concurrent unsynchronized access to shared mutable state.
- synchronized: mutual exclusion + visibility via monitor.
- volatile: visibility only, not atomicity.
- Deadlock: avoid circular lock order; use timeouts.
- Prefer atomic classes and high-level concurrency utilities.

## 19. Summary
- Synchronization prevents races by serializing access to critical sections and providing visibility.
- Use the correct tool for the job: volatile for flags, synchronized/Locks for compound actions, atomics for counters, and concurrent collections for shared structures.
- Keep locks small, avoid deadlocks, and use thread-safe abstractions for production code.

---

Keep this file for quick interview-ready revision on synchronization and race conditions.

