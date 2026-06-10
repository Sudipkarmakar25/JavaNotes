# Executor Framework

## 1. Problems with manual thread creation
- Creating threads with `new Thread()` for every task is expensive (stack, OS thread creation).
- Hard to manage lifecycle, limits, and errors.
- Leads to resource exhaustion, unpredictable throughput, and complex error handling.

## 2. What is Executor Framework
- High-level API (java.util.concurrent) to manage task execution.
- Separates task submission from execution policy.
- Provides thread pools, scheduling, and lifecycle control.

## 3. Executor interface
- Single method: `void execute(Runnable command)`.
- Lightweight abstraction for task execution.
- Implemented by `ExecutorService` and thread pools.

## 4. ExecutorService
- Sub-interface of Executor with lifecycle and result support.
- Key methods:
  - `submit(...)` returns `Future`.
  - `invokeAll()/invokeAny()` for batch tasks.
  - `shutdown()` and `shutdownNow()` to stop accepting tasks.

## 5. Thread Pool concept
- Pool reuses a fixed number of threads for many tasks.
- Reduces overhead of thread creation and improves throughput.
- Key parameters: corePoolSize, maximumPoolSize, keepAliveTime, workQueue, RejectedExecutionHandler.

## 6. FixedThreadPool
- Use case: stable number of worker threads for steady load.
- Created via `Executors.newFixedThreadPool(n)`. Backed by unbounded queue (LinkedBlockingQueue).
- Simple example:

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
pool.submit(() -> doWork());
pool.shutdown();
```

Pros/Cons:
- + Predictable concurrency level.
- - Unbounded queue may hide overload and cause OOM if tasks pile up.

## 7. CachedThreadPool
- Created via `Executors.newCachedThreadPool()`.
- Grows threads as needed, reuses idle threads, removes threads after 60s.
- Good for many short-lived asynchronous tasks.

Risks:
- Can create unbounded threads under load → resource exhaustion.

## 8. SingleThreadExecutor
- `Executors.newSingleThreadExecutor()` creates single worker thread.
- Tasks execute sequentially in FIFO order.
- Useful for serializing access to a resource.

## 9. ScheduledExecutorService
- For delayed and periodic tasks. Created via `Executors.newScheduledThreadPool(n)`.
- Key methods: `schedule()`, `scheduleAtFixedRate()`, `scheduleWithFixedDelay()`.

Example:

```java
ScheduledExecutorService sched = Executors.newScheduledThreadPool(1);
sched.scheduleAtFixedRate(() -> cleanUp(), 0, 1, TimeUnit.HOURS);
```

## 10. execute() vs submit()
- `execute(Runnable)` from `Executor`: fire-and-forget, no result.
- `submit(...)` returns `Future` (Runnable → Future<?>; Callable → Future<V>) to get result or exception.

Comparison table:

| Method | Returns | Exception handling |
|---|---:|---|
| execute | void | Uncaught exceptions handled by thread's UncaughtExceptionHandler |
| submit | Future | Exceptions captured in Future.get() |

## 11. shutdown() vs shutdownNow()
- `shutdown()`
  - Stops accepting new tasks, completes submitted tasks.
  - Use `awaitTermination()` to wait for completion.
- `shutdownNow()`
  - Attempts to cancel running tasks (interrupts threads), returns list of awaiting tasks.
  - Not guaranteed to stop running tasks immediately.

Usage pattern:

```java
pool.shutdown();
if (!pool.awaitTermination(30, TimeUnit.SECONDS)) {
  pool.shutdownNow();
}
```

## 12. Future basics
- Represents result of asynchronous computation.
- `get()` blocks until done; throws ExecutionException for exceptions.
- `isDone()`, `cancel()`, `isCancelled()` for control.
- Beware blocking on `get()` - can cause deadlocks if tasks depend on each other.

## 13. Benefits of thread pools
- Resource control: limit concurrent threads.
- Reuse threads: less overhead.
- Better throughput and latency for many small tasks.
- Centralized error handling and lifecycle management.

## 14. Real-world Spring Boot usage
- Common patterns:
  - Configure ThreadPoolTaskExecutor for async @Async methods.
  - Configure TaskScheduler for scheduled tasks.
  - Use custom Executor for Web async requests or messaging consumers.
- Example (concept): set corePoolSize, maxPoolSize, queueCapacity in Spring config.

## 15. Common mistakes
- Using `Executors` factories without tuning (unbounded queues or threads).
- Blocking inside tasks, preventing thread reuse.
- Forgetting to shutdown executors → JVM non-daemon threads keep process alive.
- Blocking on Future.get() on calling thread leading to reduced parallelism.

## 16. Best practices
- Prefer `ThreadPoolExecutor` with explicit params over default factory methods for production.
- Choose queue type carefully: bounded queue → backpressure; unbounded → risk OOM.
- Use proper RejectedExecutionHandler (CallerRunsPolicy for graceful backpressure).
- Keep tasks small and non-blocking; offload blocking I/O to dedicated pools.
- Always shutdown executors on application stop.
- Monitor pool metrics (activeCount, queue size, completedTaskCount).

## 17. Interview Quick Notes
- Executor separates task submission from execution.
- FixedThreadPool: bounded threads, unbounded queue; CachedThreadPool: unbounded threads.
- submit() → Future, execute() → void.
- shutdown() waits for tasks; shutdownNow() attempts interruption.
- Prefer ThreadPoolExecutor + bounded queue + CallerRunsPolicy for backpressure.

## 18. Summary
- Executor framework is the recommended way to manage concurrency in Java.
- Use thread pools to control resources, improve reuse, and centralize lifecycle.
- Tune pool parameters for your workload and avoid default factories in critical systems.

---

Keep this file for quick revision on Java Executor Framework.

