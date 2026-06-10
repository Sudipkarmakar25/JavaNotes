# Thread Creation In Java

## 1. What is a Thread in Java
- Definition: a thread is a lightweight unit of execution inside a process.
- Why needed:
  - Perform background work without blocking main flow (I/O, UI, servers).
  - Improve responsiveness and resource utilization.
  - Express concurrency in programs.
- Real-world examples:
  - Web server: one thread per request (or pooled workers).
  - GUI app: UI thread + worker threads for long tasks.
  - Background scheduler, logging, async I/O handlers.

## 2. Main Thread
- JVM creates the main thread when program starts.
- `public static void main(String[] args)` runs on the main thread.
- Role:
  - Starts other threads.
  - If main thread ends and only daemon threads remain, JVM exits.

## 3. Ways to Create Threads
- Two classic ways:
  1. Extend `Thread` class.
  2. Implement `Runnable` (or `Callable` for return values).
- Prefer Runnable/Callable for flexibility and composition.

## 4. Extending Thread Class
- Syntax: create subclass and override `run()`.
- `start()` creates a new OS thread and invokes `run()` on it.
- Small example:

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running in MyThread");
    }
}
// usage:
// new MyThread().start();
```

- Pros:
  - Simple to write for quick tests.
  - Can override other Thread methods if needed.
- Cons:
  - Cannot extend another class (single inheritance).
  - Mixes task with thread implementation.

## 5. Implementing Runnable
- Syntax: implement `Runnable` and pass to `Thread`.
- Cleaner separation of task and thread.
- Example:

```java
Runnable task = () -> System.out.println("Hello from Runnable");
Thread t = new Thread(task);
t.start();
```

- Lambda example (Java 8+): shown above.
- Pros:
  - Works with thread pools and executors.
  - Allows extending other classes.
  - Reusable task objects.
- Cons:
  - Slightly more indirection than extending Thread.

## 6. Thread vs Runnable (compact)

| Aspect | Thread (extend) | Runnable (implement) |
|---|---:|---:|
| Inheritance | Uses subclassing (loses other inheritance) | Can extend another class |
| Reusability | Less reusable | More reusable and composable |
| Use with pools | Not ideal | Ideal (ExecutorService) |

- Preferred approach: implement `Runnable` or `Callable` and use executors.

## 7. start() vs run()
- `start()`
  - Creates a new OS-level thread (JVM schedules it).
  - Calls `run()` internally on that new thread.
- `run()`
  - Regular method call executed on current thread; no new thread.
- Key difference: `start()` → new thread; `run()` → no new thread.

Example:

```java
Thread t = new Thread(() -> System.out.println("t: " + Thread.currentThread()));
// t.start(); // prints thread info for new thread
// t.run();   // prints current (main) thread info
```

## 8. Thread Lifecycle
- States (brief):
  - NEW: created, not started.
  - RUNNABLE: ready to run or running.
  - BLOCKED: waiting for monitor lock.
  - WAITING: waiting indefinitely (Object.wait(), join()).
  - TIMED_WAITING: waiting with timeout (sleep, wait with timeout).
  - TERMINATED: finished execution.
- Small ASCII diagram:

```
 NEW -> RUNNABLE -> (BLOCKED/WAITING/TIMED_WAITING) -> RUNNABLE -> TERMINATED
```

## 9. Important Thread Methods (short)
- `start()` — create and schedule thread.
- `run()` — task logic; don't call directly to start thread.
- `sleep(ms)` — pause current thread (static).
- `join()` — wait for another thread to finish.
- `yield()` — hint to scheduler to give up CPU (no guarantee).
- `interrupt()` — set interrupt flag; used to request cancellation.
- `Thread.currentThread()` — get reference to current thread.

## 10. Thread Scheduling
- Time-slicing: scheduler gives CPU time quanta to threads (on single-core).
- Priorities: `Thread.MIN_PRIORITY`..`MAX_PRIORITY` (hint to scheduler).
- JVM scheduling: platform-dependent; mostly relies on OS scheduler.
- Don't rely on priorities for correctness.

## 11. Daemon Thread
- Definition: background thread that does not prevent JVM exit.
- Create/set: `thread.setDaemon(true)` before `start()`.
- User thread vs daemon:
  - JVM waits for user threads to finish.
  - JVM exits when only daemon threads remain.

## 12. JVM and Threads (memory & context)
- Each thread gets its own stack for local variables and frames.
- All threads share the heap (objects, static fields).
- Context switching:
  - Involves saving/restoring CPU registers and thread state.
  - Costs CPU cycles and cache effects; keep threads coarse-grained.

## 13. Common Mistakes (quick)
- Calling `run()` instead of `start()`.
- Creating too many threads (OOM or thrashing).
- Ignoring thread-safety (race conditions on shared data).
- Using blocking calls on UI thread.

## 14. Best Practices
- Prefer `Runnable`/`Callable` + `ExecutorService`.
- Use thread pools (`Executors.newFixedThreadPool`, `newCachedThreadPool` with caution).
- Keep tasks short; avoid long synchronized blocks.
- Use higher-level concurrency utilities (`CompletableFuture`, `Semaphore`, `CountDownLatch`, `ConcurrentHashMap`).
- Handle interruption: check `Thread.currentThread().isInterrupted()` and respond.

## 15. Interview Quick Notes
- Thread: unit of execution inside process.
- Main thread runs `main()`; JVM creates it.
- Use `start()` to spawn thread; `run()` just executes method.
- Prefer `Runnable`/`Callable` and executors.
- Lifecycle: NEW → RUNNABLE → ... → TERMINATED.
- `volatile` for visibility, `synchronized` or `Lock` for mutual exclusion.
- Daemon threads don't block JVM exit.
- Avoid creating unbounded threads; use pools.

## 16. Summary
- Threads are lightweight executors; created via `Thread` or `Runnable`.
- Use executors in production.
- Keep critical sections small and respect interruption.
- Know lifecycle and common pitfalls for interviews.

---

(Cheat-sheet: keep this file for quick Java thread creation and interview revision.)

