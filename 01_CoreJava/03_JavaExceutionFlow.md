# Java Execution Flow

## End-to-End Flow
```text
Write source (.java)
   -> compile with javac
   -> generate bytecode (.class)
   -> class loading by JVM
   -> verification + linking + initialization
   -> interpreter executes bytecode
   -> JIT compiles hot code paths
   -> machine code runs on CPU
```

This is the practical lifecycle of a Java program.

---

## Step 1: Writing Source Code
You write Java source using classes, methods, fields, statements, and expressions.

Example:
```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

---

## Step 2: Compilation (`javac`)
The Java compiler:
- checks syntax
- checks type safety
- resolves symbols/imports
- generates `.class` files containing bytecode

Compilation catches **compile-time errors**, not runtime issues like `NullPointerException`.

---

## Step 3: Class Loading
When the JVM starts, it does not load every class eagerly.
It loads classes **on demand** through class loaders.

### Main class loader levels
- **Bootstrap ClassLoader** -> core JDK classes
- **Platform ClassLoader** -> platform libraries
- **Application ClassLoader** -> application classes from classpath/module path

---

## Step 4: Linking
After loading, JVM performs linking:

### 1. Verification
Checks bytecode validity and safety.

### 2. Preparation
Allocates memory for static fields and assigns default values.

### 3. Resolution
Converts symbolic references into direct references.

---

## Step 5: Initialization
Static initialization happens:
- static fields get explicit values
- static blocks execute
- class initialization occurs only once per class loader

Example:
```java
class Demo {
    static {
        System.out.println("Class initialized");
    }
}
```

---

## Step 6: `main` Method Invocation
The JVM looks for:
```java
public static void main(String[] args)
```
Why this signature?
- `public` -> accessible to JVM
- `static` -> callable without object creation
- `void` -> no return expected by JVM launcher
- `String[] args` -> command-line input

---

## Step 7: Runtime Memory Usage
During execution, JVM uses runtime areas:
- **Heap** -> objects, arrays
- **Stack** -> method frames, local variables, references
- **Metaspace/Method Area** -> class metadata
- **PC register** -> current instruction per thread
- **Native stack** -> native method execution

---

## Step 8: Interpretation and JIT
Initially bytecode may be interpreted.
Frequently executed code becomes **hot**, and the JIT compiler turns it into optimized native machine code.

This is why long-running Java applications often perform much better after warm-up.

---

## Method Call Flow
When a method is called:
1. a new stack frame is created
2. arguments are copied into that frame
3. local variables are allocated
4. bytecode instructions execute
5. frame is popped after method completes

---

## Object Creation Flow
For `new Student()`:
1. memory allocated on heap
2. fields get default values
3. constructor chain runs
4. reference returned to caller

Important: the **reference variable** may be on stack, but the **object** lives on heap.

---

## When Program Ends
The JVM exits when:
- the `main` thread finishes and
- no non-daemon threads remain alive

---

## Common Failure Points
- compile-time error -> caught by compiler
- `ClassNotFoundException` / `NoClassDefFoundError` -> class loading problems
- `ExceptionInInitializerError` -> static init failed
- runtime exceptions -> execution-time failures

---

## Interview-Specific Questions
### What happens when we run a Java program?
Source is compiled into bytecode, the JVM loads classes, verifies and initializes them, then executes bytecode using interpretation and JIT compilation.

### What is class loading?
It is the process of bringing class bytecode into memory and making it ready for execution.

### What is linking?
Linking includes verification, preparation, and resolution.

### Why is the `main` method static?
Because JVM must call it without creating an object of the class.

### When does static block execute?
During class initialization, before the class is used actively for the first time.

---

## Quick Revision
- `javac` produces bytecode.
- JVM loads -> links -> initializes classes.
- `main` is the entry point.
- JVM uses stack, heap, metaspace, PC, native stack.
- JIT improves hot-path performance.

