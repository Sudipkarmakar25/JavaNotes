# Deadlock Livelock And Starvation

## 1. Introduction
- Common concurrency problems: deadlock, livelock, starvation.
- Occur when threads coordinate incorrectly or resources are contended.
- Important for backend systems (databases, thread pools, I/O).

## 2. Deadlock
- Definition: two or more threads blocked forever, each waiting for a resource held by another.
- Real-world analogy: two people each holding one chopstick and waiting for the other.
- Key idea: circular wait for resources.

### How deadlock occurs
- Threads acquire locks on resources in conflicting order.
- One thread holds A and waits for B while another holds B and waits for A.

## 3. Conditions Required for Deadlock (Coffman conditions)
- Mutual exclusion: resource cannot be shared.
- Hold and wait: threads hold resources while waiting for others.
- No preemption: resources can't be forcibly taken.
- Circular wait: cycle of threads each waiting for next resource.

## 4. Deadlock Example (Two-thread)
Small Java example showing a simple deadlock:

```java
class DeadlockDemo {
  private final Object lockA = new Object();
  private final Object lockB = new Object();

  void thread1() {
    synchronized(lockA) {
      // hold A
      synchronized(lockB) {
        // hold B
      }
    }
  }

  void thread2() {
    synchronized(lockB) {
      // hold B
      synchronized(lockA) {
        // hold A
      }
    }
  }
}
```

Notes:
- If T1 acquires lockA and T2 acquires lockB before either gets the second lock, deadlock can occur.

## 5. Deadlock Flow (visual)

Deadlock cycle (ASCII):

```
T1 holds A -> waits for B
   ^             |
   |             v
T2 holds B <- waits for A
```

Step-by-step:
- T1 locks A
- T2 locks B
- T1 tries to lock B (blocked)
- T2 tries to lock A (blocked)
- Both blocked forever unless external action occurs

## 6. How to Prevent Deadlock
- Consistent lock ordering: always acquire locks in a fixed global order.
- Avoid nested locks when possible.
- Use tryLock with timeout (ReentrantLock.tryLock(timeout, unit)).
- Reduce lock scope: keep synchronized blocks minimal.
- Use higher-level concurrency utilities (Concurrent collections, Executors, BlockingQueue).
- Detect and recover: timeouts, retries, or kill and restart operations.

## 7. Livelock
- Definition: threads are not blocked but keep changing state in response to others and make no progress.
- Difference from deadlock: threads are active but stuck in a loop of yielding to each other.
- Real-world analogy: two people politely stepping aside repeatedly to let the other pass, so neither passes.

### Simple livelock example idea
- Two threads repeatedly retry an operation when conflict detected; both back off and retry simultaneously, repeatedly.

```java
// Pseudocode idea
while (!done) {
  if (!tryAction()) {
    backoff(); // both backoff similarly and collide again -> livelock
  }
}
```

Prevention:
- Introduce randomness in backoff.
- Use coordinated retry policies.
- Use central arbitration (queue or lock) rather than symmetric retries.

## 8. Starvation
- Definition: a thread never gets CPU time or resources it needs to proceed (indefinite postponement).
- Causes:
  - Unfair locks (always favoring other threads)
  - Busy threads with higher priority
  - Poor scheduling or resource holders never releasing locks

Examples:
- Low-priority thread never scheduled on a busy system.
- Worker thread repeatedly preempted by many short-lived tasks.

## 9. Fairness in Multithreading
- Fair scheduling: tries to give each thread a chance (e.g., fair ReentrantLock or fair queues).
- Unfair locks can be faster but risk starvation.
- Choose fair locks when starvation risk is critical; otherwise prefer performance.

## 10. Deadlock vs Livelock vs Starvation (comparison)

| Problem | Threads state | Progress? | Typical cause |
|---|---:|---:|---|
| Deadlock | Blocked (waiting for locks) | No | Circular wait / conflicting locks |
| Livelock | Runnable, active but repetitive | No useful progress | Excessive retries / symmetric backoff |
| Starvation | Runnable but not scheduled or resource-starved | No | Unfair scheduling or resource monopolization |

## 11. ReentrantLock Basics (helps avoid deadlock)
- tryLock(): attempt to acquire immediately, returns boolean.
- tryLock(timeout, unit): waits up to timeout to acquire lock.
- unlock(): release lock.
- Benefits:
  - Avoid deadlock by failing fast and backing off
  - Optionally provide fairness (new ReentrantLock(true))

Small example with tryLock timeout:

```java
ReentrantLock r1 = new ReentrantLock();
ReentrantLock r2 = new ReentrantLock();

if (r1.tryLock(100, TimeUnit.MILLISECONDS)) {
  try {
    if (r2.tryLock(100, TimeUnit.MILLISECONDS)) {
      try { /* work */ }
      finally { r2.unlock(); }
    }
  } finally { r1.unlock(); }
}
```

## 12. Detecting Deadlock
- Thread dump analysis: use jstack (or jcmd/jvisualvm) to inspect threads and locks.
- Look for WAITING/ BLOCKED threads and lock owner cycle.
- Tools: jstack, VisualVM, Mission Control.

Quick jstack tip:
- Run `jstack <pid>` and search for "Found one Java-level deadlock" or cycles of "- waiting to lock <0x...>" and "- locked <0x...>".

## 13. Real-world Examples
- Database deadlocks: two transactions lock rows in different order.
- API call chains: service A waits for service B which waits for A (rare across services but possible with sync calls).
- Banking: transfer A->B and B->A performed concurrently without ordering.

## 14. Common Mistakes
- Locking multiple resources without defined order.
- Using blocking I/O inside synchronized blocks.
- Relying on thread priorities for correctness.
- Forgetting to release locks in all code paths.

## 15. Best Practices
- Define and follow a global lock ordering.
- Minimize synchronized regions; prefer fine-grained locks only when necessary.
- Prefer non-blocking or higher-level constructs (ConcurrentHashMap, BlockingQueue, CompletableFuture).
- Use tryLock with timeout to avoid permanent blocking.
- Prefer Executors and thread pools to manage threads centrally.

## 16. Interview Quick Notes
- Deadlock: circular wait; prevent by ordering locks or timeouts.
- Livelock: active but no progress; use randomness/backoff.
- Starvation: unfair scheduling; use fair locks if needed.
- ReentrantLock.tryLock(timeout) helps avoid deadlock.
- Use thread dumps (jstack) to detect deadlocks.

## 17. Summary
- Deadlock, livelock, and starvation are distinct but all prevent progress.
- Prevent with ordering, timeouts, fairness, and higher-level concurrency utilities.
- In interviews: explain conditions (Coffman), prevention strategies, and show small code examples.

---

Keep this note for quick interview revision on multithreading problems.

