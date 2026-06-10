# Try, Catch, Finally, Throw and Throws

## 1. Keyword Overview
These keywords control how Java handles failures:
- `try` -> wraps risky code
- `catch` -> handles matching exception
- `finally` -> cleanup block
- `throw` -> explicitly throws exception object
- `throws` -> declares exception responsibility

> **Interview tip:** File 1 explains *what exceptions are*; this file explains *how code handles them*.

---

## 2. `try` Block
A `try` block contains code that may throw an exception.

### Rules
- `try` must be followed by `catch`, `finally`, or both
- `try` alone is invalid
- if exception occurs, remaining statements in `try` are skipped
- multiple statements are allowed inside one `try`

### Syntax
```text
try {
    // risky code
} catch (ExceptionType e) {
    // handling
}
```

### Example
```java
class Demo {
    void divide(int a, int b) {
        try {
            int result = a / b;
            System.out.println(result);
        } catch (ArithmeticException e) {
            System.out.println("Invalid division");
        }
    }
}
```

---

## 3. `catch` Block
A `catch` block handles an exception thrown from the associated `try` block.

### Matching rule
A `catch` matches when:
- exception type is exact match, or
- thrown exception is a child of the catch type

### Example
```text
try {
    throw new java.io.FileNotFoundException();
} catch (java.io.IOException e) {
    // matched because FileNotFoundException IS-A IOException
}
```

### Multiple `catch` blocks
Use multiple `catch` blocks when different failures need different handling.

```java
class Parser {
    void parse(String[] args) {
        try {
            String value = args[0];
            int n = Integer.parseInt(value);
            System.out.println(100 / n);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Input missing");
        } catch (NumberFormatException e) {
            System.out.println("Input must be numeric");
        } catch (ArithmeticException e) {
            System.out.println("Value must not be zero");
        }
    }
}
```

---

## 4. Catch Ordering and Multi-catch

### Child-before-parent rule
Always place more specific exception first.

```text
catch (FileNotFoundException e)
catch (IOException e)
catch (Exception e)
```

If parent comes first, child block becomes unreachable.

### Multi-catch
Java allows one block to handle multiple unrelated exception types.

```java
class MultiCatchDemo {
    void run(String[] args) {
        try {
            String input = args[0];
            int n = Integer.parseInt(input);
            System.out.println(50 / n);
        } catch (ArrayIndexOutOfBoundsException | NumberFormatException e) {
            System.out.println("Provide valid numeric input");
        } catch (ArithmeticException e) {
            System.out.println("Value must not be zero");
        }
    }
}
```

### Multi-catch rule
Do not combine parent and child in the same multi-catch.

---

## 5. `finally` Block
A `finally` block is used for cleanup logic.

### Common use cases
- closing files
- releasing DB connections
- releasing locks
- cleanup logging

### Syntax
```text
try {
    // risky code
} catch (Exception e) {
    // handling
} finally {
    // cleanup
}
```

### Important behavior
`finally` runs in most normal cases:
- exception happens or not
- exception is handled or propagated
- `return` happens in `try` or `catch`

### When `finally` may not run
- `System.exit()`
- JVM crash
- process killed forcibly

---

## 6. Return and Exception Behavior in `finally`
### `return` in `try`
```java
class ReturnDemo {
    static int test() {
        try {
            return 10;
        } finally {
            System.out.println("finally runs");
        }
    }
}
```

`finally` runs before method actually returns.

### `return` in `finally`
If `try` returns one value but `finally` also returns another value, the `finally` return wins.

```text
try returns 10
finally returns 20
actual method result -> 20
```

This is considered bad practice because it hides the original control flow.

### If `finally` throws exception
That new exception can hide the original exception.

---

## 7. `throw` Keyword
`throw` is used to explicitly throw an exception object.

### Syntax
```text
throw new ExceptionType("message");
```

### Example
```java
class UserService {
    void register(String email) {
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException("email must not be blank");
        }
    }
}
```

### Use cases
- validation
- fail-fast checks
- domain rule enforcement
- wrapping lower-level exceptions

---

## 8. `throws` Keyword
`throws` declares that a method may pass exception responsibility to caller.

### Syntax
```text
returnType methodName() throws ExceptionType {
}
```

### Example
```java
import java.io.IOException;

class FileService {
    void load() throws IOException {
        throw new IOException("File read failed");
    }
}
```

### Key point
`throws` does not throw anything by itself. It only declares possible propagation.

---

## 9. `throw` vs `throws`

| Aspect | `throw` | `throws` |
|---|---|---|
| Meaning | actually throws exception object | declares exception in method signature |
| Location | inside method/block | method declaration |
| Followed by | exception object | exception type |
| Main use | manual failure creation | propagation contract |
| Example | `throw new IOException()` | `void read() throws IOException` |

---

## 10. Nested `try-catch`
Nested `try-catch` means one exception block exists inside another.

```java
class NestedDemo {
    void run(String[] args) {
        try {
            try {
                String[] values = {"1"};
                int index = Integer.parseInt(args[0]);
                System.out.println(values[index]);
            } catch (ArrayIndexOutOfBoundsException e) {
                System.out.println("Inner handler executed");
            }

            Integer.parseInt(args[1]);
        } catch (NumberFormatException e) {
            System.out.println("Outer handler executed");
        }
    }
}
```

---

## 11. Execution Flow
```text
enter try
   ↓
exception occurs?
   ├── no  -> skip catch -> run finally -> continue
   └── yes -> matching catch found?
              ├── yes -> run catch -> run finally -> continue
              └── no  -> run finally -> propagate to caller
```

---

## 12. Best Practices
- catch specific exceptions before generic ones
- never keep empty `catch` blocks
- do not use `Exception` everywhere without reason
- avoid `return` or `throw` from `finally`
- use meaningful exception messages
- prefer try-with-resources for closeable resources

---

## 13. Interview Recap
- first matching `catch` executes
- parent catch can handle child exception
- child exception must come before parent catch
- `finally` usually runs even if `return` happens
- `throw` creates exception flow; `throws` declares it
- `try` can exist without `catch` only when `finally` is present
- one `try` statement can have only one `finally`

> Next: see `03_Try_With_Resources_Exception_Propagation_Suppressed_Exceptions.md` for advanced cleanup and propagation.
