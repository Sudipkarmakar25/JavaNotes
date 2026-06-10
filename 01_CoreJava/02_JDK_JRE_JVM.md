# JDK vs JRE vs JVM

## Big Picture
These three are related but not identical:
- **JVM** = execution engine for Java bytecode
- **JRE** = JVM + core libraries required to run Java programs
- **JDK** = JRE + development tools required to build Java programs

```text
JDK
 └── JRE
      └── JVM
```

---

## JVM
The **Java Virtual Machine** is the runtime that executes bytecode.

### Main responsibilities
- class loading
- bytecode verification
- memory management
- garbage collection
- interpretation and JIT compilation
- thread management
- security/runtime checks

### JVM internals at high level
- **Class Loader Subsystem**
- **Runtime Data Areas**: heap, stack, method area/metaspace, PC register, native stack
- **Execution Engine**: interpreter + JIT
- **GC subsystem**
- **JNI layer** for native interaction

---

## JRE
The **Java Runtime Environment** is enough to **run** Java applications.

It includes:
- JVM
- standard libraries
- runtime support files

If a machine only needs to execute an already-built Java application, historically JRE was enough.

> In modern JDK distributions, the packaging model evolved, but the conceptual distinction is still important for interviews.

---

## JDK
The **Java Development Kit** is used to **develop, compile, debug, and package** Java applications.

### Common JDK tools
- `javac` -> compiler
- `java` -> launcher/runtime entry point
- `javadoc` -> documentation generator
- `jar` -> archive packaging
- `jdb` -> debugger
- `jshell` -> REPL
- `jps`, `jstack`, `jmap`, `jcmd` -> JVM diagnostics

---

## Compile and Run Flow
```text
Developer writes .java file
   -> javac compiles source to .class bytecode
   -> java command starts JVM
   -> JVM loads and executes classes
```

---

## JVM Is an Abstract Specification
Important interview point:
- JVM is a **specification**.
- HotSpot is a **popular implementation** of that specification.
- Other implementations existed/exist, but HotSpot/OpenJDK dominates practical usage.

---

## Why JVM Matters Beyond Java
Other languages can also target the JVM:
- Kotlin
- Scala
- Groovy
- Clojure

So the JVM is a runtime platform, not just a Java-only mechanism.

---

## JDK vs JRE vs JVM Table
| Component | Purpose | Contains | Used By |
|---|---|---|---|
| JVM | Executes bytecode | execution engine, memory model, GC | runtime |
| JRE | Runs Java apps | JVM + core libraries | end user/runtime system |
| JDK | Builds and runs Java apps | JRE + dev tools | developer |

---

## Common Confusions
### Is JDK required to run Java programs?
Not always conceptually. Running needs JRE/JVM. But in practice modern installations often provide JDK directly.

### Does JVM understand Java source code?
No. JVM executes **bytecode**, not `.java` source.

### Is JRE more powerful than JDK?
No. JDK is the superset.

---

## Interview-Specific Questions
### Difference between JDK, JRE, and JVM?
JVM executes bytecode, JRE provides the environment to run Java programs, and JDK provides tools to develop and run Java programs.

### Can we run Java code without JDK?
A built Java application can run with a runtime environment, but source compilation requires JDK tools like `javac`.

### Can JVM run languages other than Java?
Yes. Any language compiled to valid JVM bytecode can run on the JVM.

### Is JVM platform dependent or independent?
The **JVM specification** is platform independent, but each JVM implementation is platform specific because it must run on a particular OS/CPU.

### Why is Java platform independent if JVM is platform specific?
Because the same bytecode can run on any platform that has a compatible JVM implementation.

---

## Quick Revision
- JVM executes bytecode.
- JRE = JVM + libraries to run apps.
- JDK = JRE + development tools.
- The interview favorite line: **JDK > JRE > JVM**.

