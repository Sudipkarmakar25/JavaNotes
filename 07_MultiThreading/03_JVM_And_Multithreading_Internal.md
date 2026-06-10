# JVM And Multithreading Internals

## Introduction
- Purpose: quick revision of JVM internals relevant to Java multithreading and backend systems.
- Focus: how JVM maps threads, memory regions, scheduling basics, costs and common pitfalls.
- Keep in mind: avoid deep OS or VM implementation details — practical interview-level view.

## JVM and OS Threads (high-level)
- JVM uses native (OS) threads in common implementations (HotSpot): each Java Thread maps to an OS thread.
- Consequence: scheduling is mostly delegated to OS scheduler; JVM provides Java-level APIs and state tracking.
- Native threads: Java thread object + underlying OS thread handle.

## How JVM Handles Threads
- Java thread model: Thread objects in heap represent execution units; actual execution runs in OS thread.
- Thread creation triggers native call to allocate OS thread and JVM bookkeeping.
- Scheduler basics: preemptive, time-sliced (platform-dependent). JVM hints (priority, daemon) are not strict guarantees.

## JVM Memory Areas (compact)
- Heap: shared among all threads; stores objects and class statics.
- Stack: per-thread; stores stack frames, local primitive variables and references.
- Method Area (Metaspace): class metadata, constants, static fields (mostly shared).
- Program Counter (PC) Register: one per thread, points to current instruction in JVM bytecode/native methods.
- Native Method Stack: used for native (JNI) calls; may be combined with Java stack depending on JVM.

ASCII: JVM memory layout

```
+-----------------------------+
|        Native Libraries    |
+-----------------------------+
|         Metaspace          |
+-----------------------------+
|            Heap            |  <- shared by threads (objects, statics)
+-----------------------------+
|   Thread Stacks (many)     |  <- each thread has its own stack frames
+-----------------------------+
|   Native Method Stacks     |
+-----------------------------+
```

## Stack Memory Per Thread
- Each Java thread has its own stack.
- Stack frame contains: local variables, operand stack, return address, method data.
- Local variables are stored in the stack frame — they are inherently not shared (thread-safe by isolation).
- Small example: method-local loop variable is safe without synchronization.

Java mini-example (local variable safety):

```java
void worker() {
  int counter = 0; // each thread gets its own 'counter' on its stack
  counter++;
}
```

## Shared Heap Memory
- Objects and static fields live in the heap and are shared between threads.
- Shared mutable state can cause race conditions; synchronize access or use atomics.
- For synchronization patterns and atomic primitives see `04_Synchronization_And_Race_Condition.md` and `09_Locks_And_Atomic_Classes.md`.

## Program Counter (PC) Register
- Each thread has its own PC to track next bytecode instruction.
- Enables independent execution of methods per thread.
- In native code or JIT, PC corresponds to native instruction pointer for that thread.

## Context Switching (concise)
- What happens:
  - OS saves current CPU registers, program counter, and thread state.
  - OS restores registers and PC for next thread.
  - JVM may update internal thread state and JIT-runtime bookkeeping.
- Cost: CPU cycles, cache flush/eviction, increased latency.
- Keep threads coarse-grained to reduce frequent switching.

Context switch flow (ASCII):

```
Thread-A running -> interrupt/timer -> save A (regs, PC) -> load B (regs, PC) -> Thread-B runs
```

## Thread Scheduling (practical)
- JVM relies on OS scheduling; Java provides Thread.priorities as hints.
- Preemptive scheduling common: OS interrupts running threads to schedule others.
- Time-slicing: each runnable thread gets time quanta on single-core; on multi-core multiple threads run simultaneously.
- Execution order unpredictable — never rely on it for correctness.

## User Thread vs Daemon Thread (table)

| Aspect | User Thread | Daemon Thread |
|---|---:|---:|
| JVM shutdown | JVM waits for completion | JVM may exit even if running |
| Use case | Application tasks | Background tasks (GC helpers, monitoring) |
| Creation | default | thread.setDaemon(true) before start() |

Example: create daemon thread:

```java
Thread t = new Thread(task);
t.setDaemon(true);
t.start();
```

## How Multithreading Improves Performance (backend focus)
- Better CPU utilization: use idle periods (I/O waits) to run other tasks.
- Parallelism on multi-core: true concurrency for compute-bound tasks.
- Responsiveness: serve requests without blocking main acceptor thread.
- Common backend examples: web servers (request handling), async IO, background workers.

## Problems & Pitfalls (brief)
- Race conditions: unsynchronized shared state.
- Deadlock: circular lock waits — avoid nested locks or use a timeout.
- Visibility issues: need volatile, synchronized, or java.util.concurrent constructs.
- Excessive threads: memory pressure (stacks), scheduling overhead, context switching thrash.

## Why Thread Creation Is Expensive
- Stack allocation per thread (default stack size can be MBs).
- OS resources: thread handle, kernel structures.
- Native calls and scheduler registration.
- Startup cost plus future context switching costs.

## Thread Pools (basics)
- Purpose: reuse threads to avoid repeated creation cost.
- ExecutorService: central Java API for pools and task submission.
- Core idea: fixed or dynamic pool, task queue, worker threads reuse.

Small Executor example:

```java
ExecutorService ex = Executors.newFixedThreadPool(10);
ex.submit(() -> doWork());
ex.shutdown();
```

## Real-world JVM Thread Examples
- Spring Boot/Tomcat: worker thread pool handles HTTP requests.
- Kafka consumers: one or more threads consuming partitions.
- Async frameworks: Netty uses event loops + worker pools.
- Database connection pools often used with thread pools for DB tasks.

## Common Interview Questions (short answers)
- Why are local variables thread-safe?
  - They live on a thread's own stack; no sharing by default.
- Why heap causes thread-safety issues?
  - Shared by all threads; concurrent access needs synchronization and visibility guarantees.
- Why is thread creation expensive?
  - Stack allocation, OS handle creation, JVM/native transitions.
- Difference: user vs daemon thread?
  - JVM waits for user threads; daemon threads don't prevent JVM exit.
- How JVM schedules threads?
  - Delegates to OS scheduler; uses preemptive, time-sliced scheduling (platform-dependent).

## Interview Quick Notes
- Each Java Thread -> OS thread (native mapping) in common JVMs.
- Stack = per thread; Heap = shared.
- Local vars are isolated; objects on heap must be synchronized when mutable.
- Context switch: save/restore registers & PC; costly (cache misses).
- Use ExecutorService to avoid thread creation cost.
- Volatile provides visibility; synchronized/Locks provide mutual exclusion + memory barrier.

## Summary
- JVM threads are native-backed execution units; understanding mapping to OS threads, per-thread stack, and shared heap is key.
- Focus on visibility, synchronization, and reducing thread creation/context switching costs in backend systems.
- For interviews: explain stack vs heap, PC-per-thread, context switching cost, and thread-pool benefits concisely.

---

Keep this file as a compact reference for JVM multithreading internals and interview prep.

