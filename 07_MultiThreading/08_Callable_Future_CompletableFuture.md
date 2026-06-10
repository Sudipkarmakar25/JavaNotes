# Callable Future And CompletableFuture

## 1. Runnable limitations
- Runnable.run() has no return value and cannot throw checked exceptions.
- Hard to get result or exception from async task.
- No built-in cancellation or composition support.

## 2. Callable interface
- `Callable<V>`: single method `V call() throws Exception`.
- Returns a value and can throw checked exceptions.
- Designed for tasks that produce results.

## 3. Callable vs Runnable (compact)
| Aspect | Runnable | Callable |
|---|---:|---:|
| Return value | none | V (generic) |
| Throws checked exceptions | no | yes |
| Used with | Thread/Executor.execute | ExecutorService.submit |

## 4. Future interface
- Represents pending result of async computation (java.util.concurrent.Future).
- Obtained by `ExecutorService.submit(Callable)` or `submit(Runnable, result)`.

## 5. Blocking behavior of Future
- `get()` blocks until computation completes.
- Can lead to blocking thread and potential deadlocks if used carelessly.
- Use `get(long, TimeUnit)` to bound wait time.

## 6. Future methods (important)
- `get()`, `get(timeout,unit)` — blocking retrieval.
- `cancel(boolean mayInterruptIfRunning)` — attempt cancel.
- `isDone()`, `isCancelled()` — status checks.

Small Callable + Future example:

```java
ExecutorService ex = Executors.newFixedThreadPool(2);
Future<Integer> f = ex.submit(() -> { return compute(); });
int result = f.get(); // blocks
ex.shutdown();
```

## 7. CompletableFuture introduction
- Modern async API (Java 8+) for non-blocking composition (java.util.concurrent.CompletableFuture).
- Implements Future and CompletionStage.
- Supports chaining, combining, exception handling, and async callbacks.

## 8. supplyAsync()
- `CompletableFuture.supplyAsync(Supplier<U>)` runs supplier async and returns CompletableFuture<U>.
- Can provide custom Executor as second argument.

Example:

```java
CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> fetchData());
```

## 9. runAsync()
- `runAsync(Runnable)` for tasks with no result.
- Returns `CompletableFuture<Void>`.

Example:

```java
CompletableFuture<Void> v = CompletableFuture.runAsync(() -> logCleanup());
```

## 10. thenApply()
- `thenApply(fn)` transforms result synchronously when previous stage completes.
- Returns new CompletableFuture with mapped value.

Example:

```java
cf.thenApply(data -> parse(data));
```

## 11. thenAccept()
- `thenAccept(consumer)` consumes result (side-effect) without returning value.
- Useful for chaining actions like updating UI or metrics.

## 12. thenCombine()
- `thenCombine(other, (a,b) -> ...)` combines results of two independent futures.
- Good for parallel API calls where results must be merged.

Example combining two async calls:

```java
CompletableFuture<Integer> f1 = supplyAsync(() -> callA());
CompletableFuture<Integer> f2 = supplyAsync(() -> callB());
CompletableFuture<Integer> combined = f1.thenCombine(f2, (a,b) -> a + b);
```

## 13. exceptionally()
- `exceptionally(fn)` handles exceptions and provides fallback value.
- Also use `handle((res,ex)->...)` for combined result+exception handling.

Example:

```java
cf.exceptionally(ex -> { log(ex); return defaultValue; });
```

## 14. Parallel API calls (pattern)
- Start independent calls with `supplyAsync()` using a shared Executor.
- Combine results with `thenCombine()` or `allOf()`.
- Wait final result via `join()` (unchecked) or `get()`.

ASCII flow (parallel calls):

```
start -> [callA] ---\
                     -> combine -> result
start -> [callB] ---/
```

## 15. Async programming flow (compact)
- Submit async tasks (supplyAsync/runAsync).
- Chain transformations (thenApply/thenCompose).
- Combine multiple futures (thenCombine/allOf/anyOf).
- Handle errors (exceptionally/handle).
- Retrieve final result non-blocking or with bounded wait.

## 16. Real-world Spring Boot examples
- Use `@Async` with configured Executor returning `CompletableFuture<T>` or `ListenableFuture`.
- Use CompletableFuture for parallel remote calls (DB, HTTP) and combine results before responding.
- Use Reactor/Project Reactor for reactive stacks; CompletableFuture remains useful in imperative services.

## 17. Future vs CompletableFuture (comparison)
| Aspect | Future | CompletableFuture |
|---|---:|---:|
| Composition | Poor | Rich chaining and combining |
| Non-blocking callbacks | No | Yes (thenApply, thenAccept) |
| Exception handling | Basic (via get()) | Built-in (exceptionally, handle) |
| Async creation | via ExecutorService | supplyAsync/runAsync (with Executor) |

## 18. Common mistakes
- Blocking on `get()` too early (defeats async benefit).
- Not providing Executor to supplyAsync → uses ForkJoinPool.commonPool which can be shared.
- Ignoring exceptions (not using exceptionally/handle).
- Overusing join() in main thread causing blocking.

## 19. Best practices
- Prefer CompletableFuture for async composition and non-blocking flows.
- Provide custom Executor tuned to workload (IO vs CPU-bound).
- Use `thenCompose()` to flatten nested futures when chaining dependent async calls.
- Use `allOf()`/`anyOf()` for multiple-future coordination and combine results carefully.
- Always handle exceptions and timeouts (`orTimeout()` / `completeOnTimeout()` in newer JDKs).

## 20. Interview Quick Notes
- Callable returns value and can throw checked exceptions.
- Future.get() blocks; prefer non-blocking composition with CompletableFuture.
- supplyAsync/runAsync start async tasks; thenApply/thenCompose chain transformations.
- thenCombine merges two futures; exceptionally handles errors.

## 21. Summary
- `Callable` + `Future` adds result/exception handling but is blocking.
- `CompletableFuture` enables non-blocking, composable async programming—useful for parallel calls and cleaner error handling.
- Tune Executors and avoid blocking to realize full benefit.

---

Keep this note for quick revision on Java async APIs.

