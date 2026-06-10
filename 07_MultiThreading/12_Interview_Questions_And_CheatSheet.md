# Multithreading Interview Questions And CheatSheet

Quick: one-page revision for common Java multithreading questions. Keep answers short and memorisable.

## 1. Thread vs Process
- Process: OS-level execution unit with separate memory (heavy).
- Thread: lightweight within process, shares heap, has own stack and PC.
- One-line: threads share memory; processes do not.

## 2. Runnable vs Callable
| Aspect | Runnable | Callable |
|---|---:|---:|
| Method | run() | call() throws Exception |
| Return | none | V |
| Used with | execute/Thread | submit (returns Future) |

One-line: use Callable when you need a result or checked exceptions.

## 3. start() vs run()
- start(): creates new thread and invokes run() on that thread.
- run(): regular method call on current thread.
- One-line: call start() to run concurrently; run() runs synchronously.

## 4. sleep() vs wait()
| Aspect | sleep() | wait() |
|---|---:|---:|
| Requires monitor | No | Yes (synchronized) |
| Releases lock | No | Yes |
| Purpose | Pause thread | Wait for condition/notify |

One-line: use wait/notify for coordination, sleep for timed pause.

## 5. notify() vs notifyAll()
- notify(): wakes one waiting thread (arbitrary choice).
- notifyAll(): wakes all waiting threads (they compete for lock).
- One-line: prefer notifyAll unless you guarantee single-waiter logic.

## 6. synchronized vs Lock
| Aspect | synchronized | Lock (ReentrantLock) |
|---|---:|---|
| Syntax | keyword | API (lock/unlock) |
| tryLock/timeout | No | Yes |
| Interruptible | No | lockInterruptibly() |
| Condition vars | wait/notify | newCondition() |

One-line: use synchronized for simple cases, Lock for advanced control.

## 7. synchronized vs volatile
- synchronized: mutual exclusion + visibility + atomicity for block.
- volatile: visibility and ordering only for single variable (no atomicity).
- One-line: volatile for flags; synchronized for compound actions.

## 8. synchronized vs AtomicInteger
- synchronized: protects arbitrary critical sections and multiple vars.
- AtomicInteger: lock-free atomic ops for single integer (CAS-based).
- One-line: prefer AtomicInteger for hot single-variable counters.

## 9. User thread vs daemon thread
- User thread: prevents JVM exit while alive.
- Daemon thread: JVM exits when only daemons remain.
- One-line: background tasks can be daemon; shutdown hooks are run on user threads.

## 10. Race condition
- Occurs when outcome depends on timing of unsynchronized accesses to shared mutable state.
- One-line: guard shared mutable data with synchronization or atomics.

## 11. Deadlock
- Four Coffman conditions (mutual exclusion, hold-and-wait, no-preemption, circular wait).
- Prevention: lock ordering, tryLock/timeouts, reduce lock scope.
- One-line: avoid circular lock dependencies.

## 12. Thread safety
- Definition: correctness under concurrent access.
- Types: immutable (always safe), thread-safe (synchronized/atomic), non-thread-safe.
- One-line: prefer immutable or confinement when possible.

## 13. ExecutorService
- High-level API to manage thread pools and task lifecycle.
- Methods: execute, submit, shutdown, shutdownNow, invokeAll.
- One-line: use Executors/ThreadPoolExecutor instead of new Thread().

## 14. Future vs CompletableFuture
| Aspect | Future | CompletableFuture |
|---|---:|---|
| Callbacks | No | Yes (thenApply, etc.) |
| Composition | Poor | Rich (thenCompose, allOf) |
| Blocking | get() blocks | join()/async chains reduce blocking |

One-line: CompletableFuture for non-blocking composition and callbacks.

## 15. ConcurrentHashMap basics
- Concurrent reads and localized writes; atomic helpers like putIfAbsent, computeIfAbsent.
- Iterators weakly consistent (no CME).
- One-line: use for concurrent maps instead of synchronized HashMap.

## 16. Volatile keyword
- Ensures visibility and ordering for a variable.
- Does not make compound actions atomic.
- One-line: volatile = visibility only.

## 17. CAS basics (Compare-And-Swap)
- Atomic primitive: compare expected value and swap to new value if equal.
- Used in Atomic classes; lock-free but may spin on contention.
- One-line: CAS enables lock-free single-variable updates.

## 18. Thread lifecycle (short)
- NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED.
- RUNNABLE may be running or ready (OS scheduler decides).
- One-line: know core states and transitions (start, wait, notify, join, interrupt).

## 19. Common coding interview questions
- Implement thread-safe counter: use AtomicInteger or synchronized increment.
- Producer-consumer: BlockingQueue or wait/notify with while-condition.
- Deadlock example: two threads locking resources in opposite order; fix with ordering.
- One-line answers: mention tool (Atomic, BlockingQueue, ExecutorService).

## 20. Frequently asked conceptual questions
- Why local variables are thread-safe? (reside on thread stack)
- Why heap needs synchronization? (shared memory)
- Difference between concurrency and parallelism? (logical vs physical)
- One-line: answer briefly with example.

## 21. One-line revision cheat sheet
- start() starts new thread; run() does not.
- synchronized protects blocks; volatile ensures visibility only.
- Future.get() blocks; use CompletableFuture for async composition.
- use BlockingQueue for producer-consumer; use Executors for thread management.
- Detect deadlocks with thread dumps (jstack).

## 22. Summary
- Keep answers short: state the concept, give a one-line definition, and mention a prevention/fix or typical API.
- Practice small code snippets: AtomicInteger, ReentrantLock.tryLock, BlockingQueue producer/consumer, CompletableFuture combine.
- Final tip: in interviews explain trade-offs (simplicity vs performance vs correctness).

---

Keep this cheat sheet for last-minute interview revision.

