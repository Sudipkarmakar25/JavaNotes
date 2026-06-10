# 01 Introduction to Multithreading

## Introduction


## CPU and Core

- CPU: Central Processing Unit — executes instructions.
- Core: Independent execution unit inside a CPU.
  - Single-core: one execution pipeline.
  - Multi-core: multiple pipelines → can run tasks truly in parallel (if OS and hardware allow).

ASCII diagram:

```
+---------------------+
| CPU                |
| +------+  +------+ |
| |Core1|  |Core2|  |  <- cores can run threads in parallel
| +------+  +------+ |
+---------------------+
```

Java context:
- JVM uses OS threads mapped to CPU cores (usually native threads).

## Program vs Process vs Thread

- Program:
  - Static code on disk (e.g., MyApp.jar).
- Process:
  - Running instance of a program.
  - Has its own memory (heap, stack, OS resources).
- Thread:
  - Lightweight execution path inside a process.
  - Shares process memory (heap) but has its own stack & registers.

Compact table:

| Feature | Process | Thread |
|---|---:|---:|
| Memory space | Separate | Shared (within process)
| Creation cost | High | Low
| Communication | IPC (slow) | Shared memory (fast)
| Failure impact | Usually isolated | Can corrupt process memory

Java note:
- `Process` (OS concept) vs `Thread` (java.lang.Thread). Threads share the same heap.

## Difference between Process and Thread (one-liners)

- Process = program in execution with isolated memory.
- Thread = a unit of execution inside a process; cheap to create and communicate.

## Multitasking and Multithreading

- Multitasking:
  - OS runs multiple processes concurrently.
  - Goal: keep CPU busy.
- Multithreading:
  - Multiple threads inside same process running concurrently.
  - Benefits: responsiveness, resource sharing, simpler design for async tasks.

Java examples (short):

- Creating a thread (Runnable):

```java
Thread t = new Thread(() -> System.out.println("Hello from thread"));
t.start();
```

- Using `ExecutorService` (recommended):

```java
ExecutorService ex = Executors.newFixedThreadPool(4);
ex.submit(() -> doWork());
ex.shutdown();
```

## Single-Core vs Multi-Core

- Single-core:
  - Only one core; OS uses time-slicing to simulate parallelism.
- Multi-core:
  - True parallel execution: different threads can run simultaneously on different cores.

Quick tip:
- More cores → better throughput for CPU-bound multi-threaded tasks.
- For I/O-bound tasks, threads improve responsiveness even on single-core.

## Time Slicing and Context Switching

- Time slicing:
  - Scheduler gives each runnable thread a time quantum.
  - Appears concurrent on single-core.
- Context switching:
  - Saving thread's CPU state and loading another's state.
  - Cost: CPU cycles + cache effects.

When it matters:
- Frequent context switches reduce throughput.
- Keep critical sections short; avoid unnecessary synchronization.

ASCII timeline (single-core):

```
Time ->
|--T1--|--T2--|--T3--|--T1--|--T2--|
```

## Concurrency vs Parallelism

- Concurrency:
  - Structure of code to handle multiple tasks that may interact.
  - May be interleaved (time-sliced) on single-core.
- Parallelism:
  - Tasks run at the same time on multiple cores.

Table:

| Term | Meaning | Example in Java |
|---|---|---|
| Concurrency | Dealing with many things at once (logical) | multiple threads sharing data
| Parallelism | Doing many things at the same time (physical) | streams().parallel() on multi-core

Short rule:
- All parallelism is concurrency; not all concurrency is parallelism.

## How Operating Systems Execute Threads (compact)

- Threads are scheduled by the OS kernel (preemptive scheduling common).
- JVM creates native threads (HotSpot uses OS threads).
- Scheduler decisions based on priorities, fairness, and policies.
- JVM maps Java threads to OS threads (1:1 model in most JVMs).

Important Java hooks:
- `Thread.yield()` — hint to scheduler (non-guaranteed).
- `Thread.sleep(ms)` — blocks thread for time slice.
- `thread.join()` — wait for thread completion.

## Real-world Examples of Multithreading (short)

- Web server: each request handled by a thread or pooled worker.
- GUI app: UI thread + background worker threads for I/O.
- Producer-Consumer: data producer thread(s) + consumer thread(s).
- Parallel data processing: map/reduce tasks using thread pools.

Mini example — Producer/Consumer (concept):

- Producer puts items into BlockingQueue.
- Consumer takes items and processes them.

Java snippet:

```java
BlockingQueue<Integer> q = new ArrayBlockingQueue<>(100);
// producer
ex.submit(() -> { q.put(1); });
// consumer
ex.submit(() -> { Integer x = q.take(); });
```

## Interview Quick Notes

- Definitions (short): CPU, Core, Program, Process, Thread.
- Process vs Thread: memory isolation vs shared heap.
- Thread creation: Thread vs Runnable vs Callable + Future.
- Executors: prefer thread pools over raw threads for production.
- Synchronization basics: `synchronized`, `volatile`, `Lock`.
  - `volatile` for visibility, not atomicity.
  - `synchronized` for mutual exclusion + memory barrier.
- Common methods: `start()`, `run()`, `join()`, `sleep()`, `interrupt()`.
- Deadlock signs & prevention:
  - Circular lock waits; avoid by lock ordering or tryLock with timeout.
- Race condition example:
  - Two threads incrementing shared counter without synchronization.
- Context switch cost: avoid extremely fine-grained locking.
- Concurrency tools: `Executors`, `CompletableFuture`, `Semaphore`, `CountDownLatch`, `CyclicBarrier`, `ConcurrentHashMap`.
- Profiling tips: use thread dumps (`jstack`) and profilers to find contention.

## Summary

- Threads: lightweight execution units sharing process memory.
- Multithreading: improves responsiveness and throughput when used correctly.
- Key trade-offs: parallelism (faster) vs complexity (synchronization, bugs).
- Practical rules:
  - Use thread pools; keep critical sections small; prefer higher-level concurrency utilities.


---

(Keep this file as a short cheat-sheet for quick revision.)

