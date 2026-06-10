# Inter Thread Communication

## 1. Introduction
- Purpose: coordinate work and share results safely between threads.
- Common uses: producer-consumer, task handoff, thread pooling coordination.

## 2. Problem Without Communication
- Busy waiting: thread polls a condition in a loop — wastes CPU.
- Resource wastage and poor latency.

## 3. wait() Method
- Purpose: suspend current thread until notified; used for condition waiting.
- Syntax: called on object monitor inside synchronized block: `obj.wait();`
- Behavior:
  - Releases the object's monitor while waiting.
  - Thread enters WAITING or TIMED_WAITING (if with timeout).
  - Must be called inside synchronized context or IllegalMonitorStateException.

## 4. notify() Method
- Purpose: wake up one thread waiting on the object's monitor.
- Syntax: `obj.notify();` inside synchronized block.
- Note: chosen thread is arbitrary (scheduler-dependent); awoken thread still competes for the monitor.

## 5. notifyAll() Method
- Purpose: wake up all threads waiting on the object's monitor.
- Safer when multiple conditions or when unsure which thread should proceed.

## 6. wait vs sleep

| Aspect | wait() | sleep() |
|---|---:|---:|
| Requires monitor | Yes (must be in synchronized) | No |
| Releases lock | Yes | No |
| Thread state | WAITING/TIMED_WAITING | TIMED_WAITING |
| Used for | Condition waiting | Pause execution |

## 7. notify vs notifyAll

| Aspect | notify() | notifyAll() |
|---|---:|---:|
| Wakes | One waiting thread | All waiting threads |
| Use when | Single waiting consumer | Multiple threads or complex conditions |

## 8. Monitor Lock Concept
- Each Java object has a monitor (lock) used by synchronized.
- wait/notify operate on the monitor's wait-set.
- A thread must own the monitor to call wait/notify/notifyAll.

Monitor diagram (simplified):

```
Thread-A   Thread-B   Thread-C
   |          |          |
 synchronized(obj)       
   |  wait() puts Thread-A -> wait-set
   |                       notify() moves one thread to runnable
```

## 9. IllegalMonitorStateException
- Occurs when wait/notify is called outside synchronized block.
- Fix: always call inside `synchronized(lock) { ... }`.

## 10. Producer Consumer Problem
- Concept: producer produces data; consumer consumes it; need coordination to avoid lost items or busy waiting.
- Real-world: web server (request producers) and worker threads (consumers).

Small Java implementation (single-slot buffer):

```java
class Buffer {
  private Integer item = null;
  public synchronized void put(int v) throws InterruptedException {
    while (item != null) wait();
    item = v; notify();
  }
  public synchronized int take() throws InterruptedException {
    while (item == null) wait();
    int v = item; item = null; notify(); return v;
  }
}
```

Notes:
- Use `while` to re-check condition after wake-up (spurious wakeups).
- Use notifyAll if multiple producers/consumers share the buffer to avoid missed wake-ups.

## 11. Thread Communication Flow
- Waiting state: threads call wait() → release lock → move to wait-set.
- Notification: notify()/notifyAll() move waiting threads to BLOCKED state (they try to re-acquire lock).
- Wake-up: once lock is available and thread acquires it, wait() returns and thread continues.

Wait-notify lifecycle (ASCII):

```
Thread -> synchronized(lock) -> check condition
  if not ok -> lock.wait() (releases lock)
 Notifier -> synchronized(lock) -> change state -> lock.notify()/notifyAll()
 Waiting threads -> compete for lock -> recheck condition -> proceed
```

## 12. Common Mistakes
- Calling wait/notify outside synchronized block -> IllegalMonitorStateException.
- Using `if` instead of `while` for condition checks -> wrong after spurious wakeup.
- Using sleep instead of wait (sleep doesn't release lock).
- Forgetting to notify -> threads stuck permanently.

## 13. Best Practices
- Always use `while(condition) wait();` pattern.
- Prefer `notifyAll()` unless you reason it's safe to use `notify()`.
- Keep synchronized blocks small and only around condition checks and updates.
- Prefer higher-level constructs (BlockingQueue) in production for clarity and safety.

## 14. Real-world Examples
- Task queue: producer threads enqueue tasks; worker threads dequeue and execute.
- Message processing: consumer waits for messages; notifier signals arrival.
- Backend request handling: handoff between acceptor thread and worker pool.

## 15. Interview Quick Notes
- wait(): releases lock, must be in synchronized, use while-condition.
- notify(): wakes one waiter; notifyAll(): wakes all.
- sleep(): does not release lock.
- Use BlockingQueue in real systems instead of manual wait/notify.

## 16. Summary
- wait/notify provide low-level thread coordination via object monitors.
- Use `while` checks, small synchronized scopes, and prefer higher-level concurrent collections where possible.
- Understand lifecycle: wait -> notified -> blocked -> runnable -> running.

---

Keep this file for quick revision on inter-thread communication and wait-notify patterns.

