# Exceptions and Hierarchy

## 1. What is an Exception?
An **exception** is an object that represents an abnormal condition during program execution.

It breaks normal flow when something unexpected happens, for example:
- invalid input parsing
- null access
- file read failure
- database failure

### Why exception handling exists
- separate business logic from failure logic
- recover from expected technical failures
- avoid abrupt termination
- return useful logs or messages

---

## 2. Exception vs Error vs Compile-time Problems

| Concept | Meaning | Example | Handling expectation |
|---|---|---|---|
| Compile-time problem | caught by compiler before run | type mismatch, syntax error | fix code before execution |
| Exception | runtime abnormal condition app may handle | `IOException`, `SQLException` | often handled or propagated |
| Error | serious JVM/system issue | `OutOfMemoryError`, `StackOverflowError` | usually not handled in business code |

### Key point
All exceptions happen at runtime, but **checked exceptions are validated by compiler rules**.

---

## 3. Exception Hierarchy
```text
Object
 └── Throwable
      ├── Error
      │    ├── OutOfMemoryError
      │    └── StackOverflowError
      └── Exception
           ├── RuntimeException
           │    ├── NullPointerException
           │    ├── ArithmeticException
           │    ├── NumberFormatException
           │    └── ClassCastException
           ├── IOException
           │    └── FileNotFoundException
           └── SQLException
```

### Meaning of important levels
- **`Throwable`** -> anything JVM allows to be thrown/caught
- **`Error`** -> serious environment/JVM failure
- **`Exception`** -> application-level failures
- **`RuntimeException`** -> unchecked exceptions, usually coding bugs or invalid state

### Why the hierarchy exists
- allows specific vs generic handling
- separates checked and unchecked behavior
- lets Java model recoverable vs non-recoverable failures

---

## 4. Types of Exceptions

| Type | Inheritance | Compiler check | Typical meaning | Examples |
|---|---|---|---|---|
| Checked | child of `Exception`, not `RuntimeException` | yes | caller may recover | `IOException`, `SQLException`, `FileNotFoundException` |
| Unchecked | child of `RuntimeException` | no | bug, bad assumption, invalid input/state | `NullPointerException`, `ArithmeticException`, `NumberFormatException` |
| Error | child of `Error` | no | JVM/system failure | `OutOfMemoryError`, `StackOverflowError` |

### Interview summary
- checked -> handle or declare
- unchecked -> compiler does not force handling
- error -> generally do not recover in normal app flow

---

## 5. Common Java Exceptions

| Exception | When it happens | Small example | Common backend scenario |
|---|---|---|---|
| `NullPointerException` | null used like object | `user.getName()` when `user` is null | missing dependency or null return |
| `ArithmeticException` | invalid arithmetic | integer divide by zero | bad calculation input |
| `ArrayIndexOutOfBoundsException` | invalid array index | `arr[5]` for size 2 | loop/index bug |
| `NumberFormatException` | invalid string to number | `Integer.parseInt("12A")` | request param parsing |
| `ClassCastException` | incompatible cast | cast `String` to `Integer` | wrong polymorphic assumption |
| `IOException` | I/O failure | file/stream operation fails | file, network, stream issues |
| `SQLException` | DB access failure | query/connection problem | repository/JDBC layer |
| `FileNotFoundException` | missing file | opening absent file | config/import file missing |

---

## 6. What Happens When Exception Occurs?
```text
statement runs
   ↓
problem happens
   ↓
exception object created
   ↓
current method checks for handler
   ↓
if not found -> moves to caller
   ↓
if nobody handles -> JVM prints stack trace
```

This movement through callers is called **stack unwinding**.

---

## 7. Interview-Focused Notes
- `Throwable` is the root of Java exception system.
- `Error` and `Exception` are siblings under `Throwable`.
- `RuntimeException` is unchecked because Java treats it as programming/runtime-state failure.
- `FileNotFoundException` is a child of `IOException`.
- You can catch `Error`, but normal business code usually should not try to recover from it.
- Exception hierarchy exists so Java can support both specific and generic handling.

---

## 8. Quick Revision
| Topic | One-line takeaway |
|---|---|
| Exception | abnormal runtime condition represented as object |
| Error | serious JVM/system failure |
| Checked exception | compiler forces handle or declare |
| Unchecked exception | compiler does not force handling |
| `Throwable` | common root for `Exception` and `Error` |
| Stack unwinding | exception moves up call stack until handled |

> Next: see `02_Try_Catch_Finally_Throw_Throws.md` for handling syntax and rules.
