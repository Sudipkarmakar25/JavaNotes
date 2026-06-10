# Multithreading In Spring Boot

## 1. How Spring Boot handles requests
- Default (Servlet-based) apps use a thread-per-request model provided by the servlet container (Tomcat/Jetty/Undertow).
- Each HTTP request is handled on a worker thread from the container thread pool.
- Controller code runs on that thread unless explicitly offloaded.

## 2. Thread-per-request model
- Simple and predictable: one request → one thread.
- Limits: throughput bounded by thread pool size; blocking I/O on request thread reduces concurrency.
- Good for CPU-bound work if threads are sized properly.

### Request flow (simplified ASCII)

```
Client -> Connector -> Tomcat Worker Thread -> DispatcherServlet -> Controller
```

If controller calls @Async or offloads to Executor:

```
Controller -> submit task to Executor -> immediate response or wait for Future
```

## 3. Tomcat thread pool basics
- Tomcat uses an Executor (maxThreads, minSpareThreads) configured in application.properties or server.xml.
- Key properties (Spring Boot `application.properties`):
  - `server.tomcat.threads.max` (maxThreads)
  - `server.tomcat.threads.min-spare`
- Tune based on workload: CPU-bound → roughly cores * (1 + expectedWaitRatio); I/O-bound → higher.

## 4. @Async annotation
- Enables running a method in a separate thread provided by a configured Executor.
- Return types: `void`, `Future<T>`, `CompletableFuture<T>`.
- Must be used on public methods called from a different bean (proxy-based).

Small example:

```java
@Service
public class EmailService {
  @Async("ioExecutor")
  public CompletableFuture<Boolean> sendEmail(Email e) { ... }
}
```

## 5. Enabling async support
- Add `@EnableAsync` on a @Configuration class (or on main app) and declare Executor beans.

Example ThreadPoolTaskExecutor bean:

```java
@Bean(name = "ioExecutor")
public ThreadPoolTaskExecutor ioExecutor() {
  ThreadPoolTaskExecutor e = new ThreadPoolTaskExecutor();
  e.setCorePoolSize(10);
  e.setMaxPoolSize(50);
  e.setQueueCapacity(200);
  e.setThreadNamePrefix("io-");
  e.initialize();
  return e;
}
```

## 6. CompletableFuture with Spring Boot
- Use `CompletableFuture` for composing async tasks and combining parallel calls.
- Return `CompletableFuture<T>` from @Async methods or use `CompletableFuture.supplyAsync(..., executor)`.

Controller example returning async result:

```java
@GetMapping("/data")
public CompletableFuture<ResponseEntity<Data>> getData() {
  return dataService.fetchAsync().thenApply(d -> ResponseEntity.ok(d));
}
```

## 7. Async service methods
- Guidelines:
  - Keep async methods focused and non-blocking where possible.
  - Avoid calling @Async methods from same bean (proxy won't apply).
  - Handle exceptions (use CompletableFuture.exceptionally or handle()).

## 8. Scheduled tasks basics
- Use `@Scheduled` for periodic tasks; these run on a scheduler thread pool (configure `TaskScheduler`).
- Define `@EnableScheduling` and a `ThreadPoolTaskScheduler` for concurrency.

Example:

```java
@Scheduled(fixedDelay = 60000)
public void cleanup() { ... }
```

ThreadPoolTaskScheduler bean:

```java
@Bean
public ThreadPoolTaskScheduler taskScheduler() {
  ThreadPoolTaskScheduler s = new ThreadPoolTaskScheduler();
  s.setPoolSize(4);
  return s;
}
```

## 9. ThreadPoolTaskExecutor
- Spring wrapper over ThreadPoolExecutor; integrates with lifecycle and metrics.
- Configure corePoolSize, maxPoolSize, queueCapacity, rejectedExecutionHandler.
- Monitor `activeCount`, `queue.size()` and tune for workload.

Note: For general executor and thread-pool concepts see `07_Executor_Framework.md` (pool types, execute/submit, shutdown patterns).

Compact config tips:
- CPU-bound: core ~= #cores.
- I/O-bound: increase core threads based on wait ratio.
- Use bounded queue + CallerRunsPolicy to apply backpressure.

## 10. Parallel API calls
- Pattern: run independent remote calls in parallel using `CompletableFuture.supplyAsync()` with a dedicated IO Executor, then combine with `thenCombine()` or `allOf()`.
- Avoid using common ForkJoinPool for blocking I/O; provide dedicated IO executor to prevent starvation.

## 11. Kafka consumers and threads
- KafkaConsumer is not thread-safe; use one consumer per thread or use Kafka listener containers which manage concurrency.
- In Spring Kafka, set `concurrency` on the `ConcurrentMessageListenerContainer` to process partitions in parallel.

## 12. Database connection pools basics
- DB pools (HikariCP, TomcatCP) limit concurrent DB connections; match pool size with thread pool usage.
- Over-sized thread pools with small DB pool cause threads to block waiting for a connection → increased latency.
- Tune DB pool and executor together.

## 13. Performance optimization basics
- Measure first: use metrics (Micrometer), thread dumps, and profiling.
- Reduce blocking on request threads: offload blocking I/O to dedicated pools.
- Use bounded queues for backpressure and avoid OOM.
- Tune GC and heap separately; observe throughput and tail latency.

## 14. Common mistakes
- Not shutting down custom executors → prevents graceful shutdown.
- Using default `ForkJoinPool.commonPool()` for blocking tasks.
- Calling @Async methods from same class (no proxy).
- Mismatched sizes between thread pools and DB/IO resources.

## 15. Best practices
- Centralize executor configuration (properties) and reuse executors for similar workloads.
- Use bounded queues and a sensible RejectedExecutionHandler (e.g., CallerRunsPolicy).
- Prefer CompletableFuture composition over blocking `get()`.
- Instrument thread pools and add health checks for queue saturation.
- For high throughput, consider reactive stacks (WebFlux) but only if the team and infrastructure support it.

## 16. Real-world backend examples
- File upload processing: accept request, persist metadata, offload file processing to IO executor, respond early.
- Aggregating remote services: parallel calls using CompletableFuture and combine results for response.
- Scheduled cleanup and metrics reporting using ThreadPoolTaskScheduler.

## 17. Interview Quick Notes
- Spring Boot request uses container threads (Tomcat) — tune `maxThreads`.
- Use `@Async` + `ThreadPoolTaskExecutor` for background tasks; return `CompletableFuture` for composition.
- Provide dedicated IO executor for blocking remote calls; avoid commonPool for blocking work.
- Tune DB pool and executor together to avoid connection waits.

## 18. Summary
- Understand thread-per-request model, offloading strategies, and how to configure executors in Spring Boot.
- Use CompletableFuture for parallelism and ThreadPoolTaskExecutor for controlled threading.
- Monitor and tune thread pools together with external resources (DB, remote services).

---

Keep this note for quick interview and revision on multithreading in Spring Boot.

