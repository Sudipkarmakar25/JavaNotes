# Core Spring Concepts
## Dependency Injection, IoC, ApplicationContext vs BeanFactory, Circular Dependency

---

# 1️⃣ Inversion of Control (IoC)

## What is IoC?

Inversion of Control means:

> The control of object creation and lifecycle is given to the framework, not the developer.

---

## Without IoC

```java
public class Car {

    private Engine engine = new Engine();

}
```

Here:

- Car creates Engine.
- Car controls Engine lifecycle.
- Tight coupling.

Car is responsible for creating its dependency.

---

## With IoC

```java
public class Car {

    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

Now:

- Car does NOT create Engine.
- Engine is given from outside.

Control is inverted.

---

## Who Has Control Now?

Spring Container.

This container:

- Creates objects (Beans)
- Injects dependencies
- Manages lifecycle

That is IoC.

---

# 2️⃣ Dependency Injection (DI)

Dependency Injection is the mechanism that implements IoC.

Definition:

> DI is the process of providing dependencies to a class from external sources.

---

## Types of Dependency Injection

### 1️⃣ Constructor Injection (Recommended)

```java
@Service
public class UserService {

    private final UserRepository repo;

    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}
```

✔ Immutable  
✔ Thread-safe  
✔ Mandatory dependencies enforced

---

### 2️⃣ Setter Injection

```java
@Autowired
public void setRepo(UserRepository repo) {
    this.repo = repo;
}
```

Used when dependency is optional.

---

### 3️⃣ Field Injection (Not Recommended)

```java
@Autowired
private UserRepository repo;
```

❌ Hard to test  
❌ Not immutable

---

## How Spring Performs DI Internally

During container startup:

1. Spring scans classes.
2. Creates BeanDefinitions.
3. Instantiates objects using reflection.
4. Resolves constructor parameters.
5. Injects dependencies.
6. Stores bean in singleton cache.

DI happens during bean creation phase.

---

# 3️⃣ BeanFactory vs ApplicationContext

Both are IoC containers.

But they are not equal.

---

# 3.1 BeanFactory

Core container.

Interface:

```
org.springframework.beans.factory.BeanFactory
```

Responsibilities:

- Create beans
- Inject dependencies
- Manage bean lifecycle

Minimal container.

Lazy loading by default.

Beans created only when requested.

---

# 3.2 ApplicationContext

Advanced container.

Interface:

```
org.springframework.context.ApplicationContext
```

Extends BeanFactory.

Adds:

- Event publishing
- Internationalization
- AOP integration
- Environment abstraction
- Automatic BeanPostProcessor registration

Eager loading by default.

Singleton beans created during startup.

---

## Practical Difference

| Feature | BeanFactory | ApplicationContext |
|----------|-------------|-------------------|
| Basic DI | ✅ | ✅ |
| BeanPostProcessors | Manual | Automatic |
| Events | ❌ | ✅ |
| AOP Support | Limited | Full |
| Bean Loading | Lazy | Eager |

In Spring Boot:

ApplicationContext is always used.

---

# 4️⃣ Circular Dependency

## What Is Circular Dependency?

When two beans depend on each other.

Example:

```java
@Service
class A {
    @Autowired
    private B b;
}

@Service
class B {
    @Autowired
    private A a;
}
```

A needs B.
B needs A.

This creates a dependency cycle.

---

# 4.1 Why This Is Problematic

To create A → Spring needs B.  
To create B → Spring needs A.

Which one should be created first?

Logical deadlock.

---

# 4.2 How Spring Solves It (Setter/Field Injection Only)

Spring uses a 3-level cache:

1️⃣ singletonObjects  
2️⃣ earlySingletonObjects  
3️⃣ singletonFactories

Process:

1. Create A (without full initialization).
2. Expose early reference of A.
3. Create B.
4. Inject early reference of A into B.
5. Finish initialization.

This works only if object can be partially constructed first.

---

# 4.3 Why Constructor Injection Fails

Example:

```java
class A {
    private final B b;
    public A(B b) { this.b = b; }
}

class B {
    private final A a;
    public B(A a) { this.a = a; }
}
```

Now:

To create A → B must exist.  
To create B → A must exist.

Neither can be partially created.

Spring throws:

```
BeanCurrentlyInCreationException
```

Constructor injection prevents circular dependencies.

This is good.

It forces better design.

---

# 5️⃣ Summary

## IoC
Framework controls object creation.

## Dependency Injection
Mechanism to supply dependencies.

## BeanFactory
Basic IoC container.

## ApplicationContext
Advanced IoC container used in Spring Boot.

## Circular Dependency
Occurs when two beans depend on each other.
Resolved only in setter/field injection.
Fails in constructor injection.

---

