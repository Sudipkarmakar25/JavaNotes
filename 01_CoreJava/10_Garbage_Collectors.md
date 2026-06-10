# Garbage Collection in Java

## What Is Garbage Collection?
Garbage Collection (GC) is the JVM process of automatically reclaiming memory occupied by objects that are no longer reachable.

Important: GC frees **heap memory**, not stack variables directly.

---

## Core Idea: Reachability, Not Just "No Reference Variable"
An object becomes eligible for GC when it is **not reachable** from GC roots.

### Common GC roots
- local variables in active stack frames
- static fields
- active threads
- JNI references

If no path exists from any GC root to the object, it is collectible.

---

## Basic Example
```java
Student s = new Student();
s = null; // old object may become eligible for GC
```

Eligible for GC does **not** mean immediately collected.

---

## Common Ways Objects Become Eligible
- nullifying reference
- reassigning reference
- anonymous object with no reference
- isolated object graph
- method scope ends and no other references exist

---

## Generational Heap Model
Most JVM GCs use generational ideas:
- **Young Generation** -> newly created objects
- **Old/Tenured Generation** -> long-lived objects

Why?
Because most objects die young.

This is called the **weak generational hypothesis**.

---

## Minor GC vs Major/Full GC
### Minor GC
Cleans young generation.
Usually faster and more frequent.

### Major/Full GC
Cleans old generation and may involve larger pause/compaction work.
Usually more expensive.

---

## Stop-The-World
Many GC phases pause application threads temporarily.
This is called **Stop-The-World (STW)**.

Good collectors try to reduce pause impact, not eliminate GC entirely.

---

## Major GC Algorithms

### 1. Copying Algorithm (sometimes informally called Copy-and-Swap)
This algorithm divides memory into two regions:
- **from-space**
- **to-space**

Objects are allocated in one region. When GC runs:
1. live objects are identified
2. only live objects are copied to the other region
3. the roles of the two regions are swapped

### Why it is useful
- very fast when most objects die young
- naturally compacts memory
- no fragmentation after collection

### Trade-offs
- needs extra memory because one region must stay free for copying
- expensive if too many objects survive

This idea is commonly used in **young generation collectors**.

> Interview note: this is different from **CAS (Compare-And-Swap)**, which is a concurrency primitive, not a garbage collection algorithm.

### 2. Mark-Sweep
This is one of the classic GC algorithms.

It works in two broad phases:
1. **Mark** -> start from GC roots and mark all reachable objects
2. **Sweep** -> scan memory and reclaim unmarked objects

### Why it is useful
- does not require extra half-space like copying GC
- conceptually simple

### Trade-offs
- leaves memory fragmentation
- allocation may become harder over time because free space is scattered

### Key interview idea
Mark-Sweep reclaims garbage, but it does **not** rearrange surviving objects.

### 3. Mark-Sweep-Compact (Mark-Compact)
This algorithm adds one more step to Mark-Sweep:
1. mark live objects
2. sweep dead objects logically / identify free space
3. **compact** surviving objects so they are moved together

### Why it is useful
- removes fragmentation
- makes future allocation easier
- useful for long-lived object regions such as old generation

### Trade-offs
- compaction means object movement
- moving objects takes extra time and may increase pause cost

### Key interview idea
Mark-Sweep-Compact is slower than plain Mark-Sweep in some situations, but it gives a cleaner heap layout.

---

## Algorithm Comparison

| Algorithm | Main Idea | Fragmentation | Extra Memory | Typical Strength |
|---|---|---|---|---|
| Copying | copy live objects to another region | No | Yes | very efficient when most objects die young |
| Mark-Sweep | mark live, sweep dead | Yes | No major extra half-space | simpler, space-efficient |
| Mark-Sweep-Compact | mark, reclaim, then compact live objects | No | lower than copying half-space, but movement cost exists | better long-term heap layout |

### Easy memory rule
- **Copying** -> fastest for many short-lived objects
- **Mark-Sweep** -> simple but can fragment memory
- **Mark-Sweep-Compact** -> reduces fragmentation but adds movement cost

---

## Common Garbage Collectors (high level)
- **Serial GC** -> simple, single-threaded GC
- **Parallel GC** -> throughput focused
- **G1 GC** -> balanced, common modern default
- **ZGC / Shenandoah** -> low pause-time focused

Interview tip: explain trade-off as **throughput vs latency vs footprint**.

---

## `System.gc()`
You can request GC:
```java
System.gc();
```
But JVM is free to ignore or delay it. It is only a hint, not a guarantee.

---

## `finalize()`
`finalize()` was historically intended for cleanup before object collection.
It is deprecated and should be avoided because it is unpredictable and harmful for performance/reliability.

Use:
- try-with-resources
- `AutoCloseable`
- explicit cleanup

---

## Reference Types and GC
In Java, a **reference** is the handle-like value used to access an object. The reference is not the object itself; it is the path through which the JVM and your code reach that object.

GC decides object lifetime based on **reachability**. If an object can still be reached from GC roots through valid references, it stays alive. If that reachability is lost, the object becomes eligible for collection.

### Reference types
- **Strong reference** -> the normal default reference in Java. As long as an object is strongly reachable, it will not be garbage collected.
- **Soft reference** -> a weaker reference used for memory-sensitive retention. The object may stay alive for some time, but JVM can clear it when memory pressure increases.
- **Weak reference** -> an even weaker reference that does not strongly keep the object alive. If an object is only weakly reachable, it can usually be collected in the next GC cycle.
- **Phantom reference** -> the weakest reference type, mainly used for advanced cleanup tracking after the object has become unreachable. It is not used for normal object access.

Common interview link: `WeakHashMap` uses weak keys.

---

## Memory Leak in Java?
Yes, Java can still leak memory.
Not by manual free failure, but by keeping references longer than needed.

Examples:
- static collections growing forever
- listener not removed
- caches without eviction
- thread-local misuse

---

## Interview-Specific Questions
### Is garbage collection guaranteed immediately after object becomes unreachable?
No. The object only becomes eligible for GC. Actual collection timing is decided by JVM.

### Can we force garbage collection?
No. We can only request it using `System.gc()`.

### Does Java have memory leaks?
Yes. If objects remain reachable unintentionally, memory can keep growing.

### What is the difference between minor GC and major/full GC?
Minor GC targets young generation; major/full GC involves older generations and is usually costlier.

### Why is `finalize()` discouraged?
Because it is unpredictable, slow, and not reliable for resource cleanup.

### What is the difference between Copying GC and Mark-Sweep?
Copying GC moves only live objects to a new region and avoids fragmentation, while Mark-Sweep marks live objects and sweeps dead ones but may leave fragmentation.

### Why is Mark-Sweep-Compact preferred over plain Mark-Sweep in some cases?
Because compaction reduces fragmentation and creates contiguous free memory, which makes future allocation easier.

### Why is copying collection often used in young generation?
Because most young objects die quickly, so copying only a small number of survivors is efficient.

### Is Compare-And-Swap a garbage collection algorithm?
No. Compare-And-Swap (CAS) is a lock-free concurrency primitive. In GC discussions, the relevant algorithm is usually the **Copying** approach.

---

## Quick Revision
- GC works on reachability from GC roots.
- Eligible for GC != immediately removed.
- Young objects die fast; hence generational GC.
- Copying avoids fragmentation but needs extra space.
- Mark-Sweep is simple but can fragment memory.
- Mark-Sweep-Compact removes fragmentation by moving live objects.
- `System.gc()` is only a request.
- Java memory leaks happen through unwanted references.

