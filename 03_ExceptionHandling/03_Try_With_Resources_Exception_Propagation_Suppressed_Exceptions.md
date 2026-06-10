# Try-With-Resources, Exception Propagation and Suppressed Exceptions

## 1. Why Try-With-Resources Was Introduced
Before Java 7, resources were usually closed in `finally` blocks.

That approach had problems:
- verbose cleanup code
- easy-to-miss resource closing
- multiple nested cleanup blocks
- original exception could be hidden by `close()` failure

### Goal of try-with-resources
- automatic cleanup
- cleaner syntax
- safer failure reporting
- support for suppressed exceptions

---

## 2. What is a Resource?
A **resource** is any object that must be closed or released after use.

### Common examples
| Resource | Typical risk if not closed |
|---|---|
| `FileInputStream` | open file handle |
| `BufferedReader` | file/stream leak |
| JDBC `Connection` | connection pool exhaustion |
| `Socket` | network handle leak |

### Resource leak
A resource leak is not always a heap-memory leak. Many resources live outside normal Java heap but still exhaust OS or DB limits.

---

## 3. Try-With-Resources Basics
A try-with-resources statement declares resources inside the `try` header.

### Syntax
```text
try (ResourceType r = create()) {
    // use resource
}
```

### Example
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

class ReaderDemo {
    void read() throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
            System.out.println(br.readLine());
        }
    }
}
```

### Rules
- resource must implement `AutoCloseable`
- resource variable is effectively final
- `close()` is called automatically
- closing happens even if exception occurs in `try`

---

## 4. `AutoCloseable` vs `Closeable`

| Aspect | `AutoCloseable` | `Closeable` |
|---|---|---|
| Package | `java.lang` | `java.io` |
| Method | `close() throws Exception` | `close() throws IOException` |
| Purpose | generic resource contract | I/O-focused resource contract |
| Typical use | custom resources, JDBC, framework helpers | streams, readers, writers |
| Relation | parent | child |

### Key point
Every `Closeable` is an `AutoCloseable`, but not every `AutoCloseable` is a `Closeable`.

---

## 5. Internal Working of Try-With-Resources
Try-with-resources is largely a **compiler-assisted cleanup feature**.

### Conceptual translation
```text
try (Resource r = open()) {
    use(r);
}
```

behaves like simplified cleanup logic:
```text
Resource r = open();
try {
    use(r);
} finally {
    if (r != null) {
        r.close();
    }
}
```

### Extra logic compiler also handles
- primary exception from `try`
- exception from `close()`
- suppressed exceptions
- multiple resources in reverse closing order

---

## 6. Multiple Resources and Closing Order
You can declare multiple resources in one statement.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

class MultipleResourcesDemo {
    void copyFirstLine() throws IOException {
        try (
            BufferedReader br = new BufferedReader(new FileReader("input.txt"));
            FileWriter writer = new FileWriter("output.txt")
        ) {
            String line = br.readLine();
            writer.write(line == null ? "" : line);
        }
    }
}
```

### Closing order
Resources close in **reverse order of declaration**.

```text
try (r1; r2; r3) { ... }
close order -> r3, r2, r1
```

---

## 7. Suppressed Exceptions
A **suppressed exception** is a close-time exception attached to the main exception instead of replacing it.

### Why it was needed
Before Java 7:
```text
try body throws A
finally close() throws B
visible exception becomes B
A may be lost
```

With try-with-resources:
- exception from `try` becomes primary
- exception from `close()` becomes suppressed

### Example
```java
class FailingResource implements AutoCloseable {
    void work() {
        throw new RuntimeException("Failure in work()");
    }

    @Override
    public void close() {
        throw new RuntimeException("Failure in close()");
    }
}

class SuppressedDemo {
    void run() {
        try (FailingResource resource = new FailingResource()) {
            resource.work();
        } catch (RuntimeException e) {
            System.out.println("Main: " + e.getMessage());
            for (Throwable t : e.getSuppressed()) {
                System.out.println("Suppressed: " + t.getMessage());
            }
        }
    }
}
```

### Important API
```text
Throwable[] getSuppressed()
```

---

## 8. Exception Propagation
**Propagation** means exception moves to caller when not handled in current method.

```text
m3() throws
   ↓
m2() does not handle
   ↓
m1() does not handle
   ↓
caller handles or JVM prints stack trace
```

### Example
```java
import java.io.IOException;

class PropagationDemo {
    static void m3() throws IOException {
        throw new IOException("Disk error");
    }

    static void m2() throws IOException {
        m3();
    }

    static void m1() throws IOException {
        m2();
    }

    void run() {
        try {
            m1();
        } catch (IOException e) {
            System.out.println("Handled in run()");
        }
    }
}
```

### Checked vs unchecked propagation
| Type | Compiler rule | Example |
|---|---|---|
| Checked | catch or declare | `IOException` |
| Unchecked | no forced handling | `IllegalArgumentException` |

This movement through callers is called **stack unwinding**.

---

## 9. Rethrowing and Wrapping

### Rethrowing same exception
```java
import java.io.IOException;

class RethrowDemo {
    void process() throws IOException {
        try {
            throw new IOException("Low-level issue");
        } catch (IOException e) {
            System.out.println("Logging before rethrow");
            throw e;
        }
    }
}
```

### Wrapping exception
```java
import java.io.IOException;

class WrappingDemo {
    void service() {
        try {
            throw new IOException("File corrupted");
        } catch (IOException e) {
            throw new RuntimeException("Import failed", e);
        }
    }
}
```

### Why wrapping is used
- adds business context
- preserves cause chain
- hides low-level implementation details from upper layer

---

## 10. Custom Exceptions
Custom exceptions give domain meaning to failures.

### Checked custom exception
```java
class PaymentFailedException extends Exception {
    PaymentFailedException(String message) {
        super(message);
    }
}
```

### Unchecked custom exception
```java
class InvalidConfigurationException extends RuntimeException {
    InvalidConfigurationException(String message) {
        super(message);
    }
}
```

### When to use
- checked -> caller should consciously handle/propagate
- unchecked -> invalid state, configuration, or programming misuse

---

## 11. Best Practices
- prefer try-with-resources over manual cleanup
- never swallow exceptions silently
- log with business context
- preserve original cause while wrapping
- keep custom exception hierarchy small and meaningful

---

## 12. Interview Recap
- try-with-resources automatically closes `AutoCloseable` resources
- `close()` exceptions become suppressed when a primary exception already exists
- resources close in reverse order of declaration
- propagation means exception moves upward; handling means it is caught
- checked exceptions are compiler-enforced during propagation
- wrapping is used to add context without losing the original cause

## 13. Quick Revision
| Topic | One-line takeaway |
|---|---|
| try-with-resources | automatic cleanup for `AutoCloseable` resources |
| resource leak | acquired resource not released |
| `AutoCloseable` | base cleanup contract |
| `Closeable` | I/O-focused subinterface |
| suppressed exception | close failure attached to primary failure |
| propagation | exception moves to caller |
| wrapping | higher-level exception keeps lower-level cause |
| custom exception | domain-specific exception type |
