# Spring Boot Starters & Dependency Management

---

## 1️⃣ What Are Spring Boot Starters?

Before Spring Boot:

You manually added:

- spring-core
- spring-context
- spring-beans
- spring-web
- spring-webmvc
- jackson-databind
- validation-api
- logging libraries
- embedded server
- etc...

And you had to ensure versions were compatible.

This caused:
- Dependency conflicts
- ClassNotFoundException
- Version mismatch issues

---

## 2️⃣ What Is a Starter?

A **Starter** is a dependency descriptor that groups compatible libraries.

Example:

```
spring-boot-starter-web
```

Includes:

- spring-web
- spring-webmvc
- jackson
- validation
- embedded Tomcat
- logging

You add ONE dependency.
It pulls many internally.

---

## 3️⃣ Common Starters

### Web
```
spring-boot-starter-web
```

### JPA
```
spring-boot-starter-data-jpa
```

### Security
```
spring-boot-starter-security
```

### Test
```
spring-boot-starter-test
```

---

## 4️⃣ How Version Management Works (BOM)

Spring Boot uses a:

```
Bill Of Materials (BOM)
```

Defined in:

```
spring-boot-dependencies
```

This ensures:

- All dependencies use compatible versions
- You don’t specify versions manually

Maven:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

---

#  Bean Types & Bean Lifecycle

---

# 1️⃣ What Is a Bean?

A Bean is:

> An object managed by Spring IoC container.

It is created, configured, wired, and destroyed by Spring.

---

# 2️⃣ Types of Beans (By Scope)

## Singleton (Default)

One instance per container.

```
@Scope("singleton")
```

## Prototype

New instance every time requested.

```
@Scope("prototype")
```

## Request Scope (Web)

One per HTTP request.

```
@Scope("request")
```

## Session Scope

One per HTTP session.

---

# 3️⃣ Bean Lifecycle (Extremely Important)

The lifecycle:

1. Bean Definition loaded
2. Bean instantiated
3. Dependencies injected
4. BeanPostProcessor before init
5. @PostConstruct executed
6. InitializingBean.afterPropertiesSet()
7. Custom init-method
8. BeanPostProcessor after init
9. Bean ready
10. @PreDestroy executed before shutdown

---

## Example:

```java
@Component
public class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("Bean destroyed");
    }
}
```

---

# 4️⃣ Bean Lifecycle Internals

Internally:

Spring uses:

- BeanFactory
- ApplicationContext
- BeanDefinition
- BeanPostProcessor
- InstantiationStrategy

Core interface:

```
org.springframework.beans.factory.BeanFactory
```

Advanced container:

```
ApplicationContext
```

ApplicationContext = BeanFactory + additional features.

---

# Core Spring & Boot Annotations

---

# 1️⃣ Stereotype Annotations

## @Component
Generic bean.

## @Service
Business layer (semantic specialization of @Component).

## @Repository
Data layer + exception translation.

## @Controller
MVC controller.

## @RestController
@Controller + @ResponseBody.

---

# 2️⃣ Dependency Injection

## Constructor Injection (Recommended)

```java
@Service
public class UserService {

   private final UserRepository repo;

   public UserService(UserRepository repo) {
       this.repo = repo;
   }
}
```

## @Autowired

Can be used on:

- Constructor
- Field
- Setter

Constructor injection preferred for immutability.

---

# 3️⃣ Configuration Annotations

## @Configuration

Defines configuration class.

## @Bean

Defines bean manually.

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyService();
    }
}
```

---

# 4️⃣ Spring Boot Specific

## @SpringBootApplication

Combines:

- @Configuration
- @ComponentScan
- @EnableAutoConfiguration

---

## @ConfigurationProperties

Binds properties from application.properties.

---

# 
Complete Journey of a Request

---

Let’s trace:

```
GET /users/1
```

---

## Step 1 — Embedded Tomcat Accepts Request

Tomcat:
- Accepts TCP connection
- Uses thread pool
- Parses HTTP
- Creates HttpServletRequest
- Creates HttpServletResponse

---

## Step 2 — DispatcherServlet

Auto-configured by Spring Boot.

All requests go here.

---

## Step 3 — HandlerMapping

Maps:

```
/users/1 → UserController.getUser()
```

---

## Step 4 — HandlerAdapter

Invokes controller method using reflection.

---

## Step 5 — Argument Resolution

Spring resolves:

- @PathVariable
- @RequestParam
- @RequestBody

Using HandlerMethodArgumentResolver.

---

## Step 6 — Controller Execution

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return service.findById(id);
}
```

---
