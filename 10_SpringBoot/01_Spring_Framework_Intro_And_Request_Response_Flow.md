# Spring & Spring Boot – Complete Lecture (Part 1)
## From Servlets to Spring Framework

---

# 1️⃣ What Is a Web Application?

At the most fundamental level, a web application is just a function:

```
Response = f(Request)
```

More formally:

```
f: HTTP Request → HTTP Response
```

That’s it.

Everything else — frameworks, containers, annotations — exists to make this transformation easier, safer, and scalable.

---

# 2️⃣ What Is a Servlet?

A **Servlet** is simply a Java class that handles HTTP requests.

Typically, we extend:

```java
import javax.servlet.http.HttpServlet;

public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
                         throws IOException {

        response.getWriter().write("Hello World");
    }
}
```

A servlet:
- Receives request
- Processes logic
- Sends response

But it is NOT standalone.

---

# 3️⃣ What Is a Servlet Container?

A Servlet does not run by itself.

It is managed by a **Servlet Container**.

Examples:
- Apache Tomcat
- Jetty

The container:
- Listens on a port (e.g., 8080)
- Accepts HTTP connections
- Converts raw bytes into `HttpServletRequest`
- Creates `HttpServletResponse`
- Calls your servlet methods (`doGet`, `doPost`)
- Manages threads
- Manages lifecycle

This introduces an important concept:

## Inversion of Control (IoC)

You do NOT call the framework.  
The framework calls you.

---

# 4️⃣ What Happens When a Request Comes?

Let’s trace it step by step.

### Step 1: Browser Sends HTTP Request

```
GET /hello HTTP/1.1
Host: localhost:8080
```

### Step 2: OS receives packet
TCP stack processes it.

### Step 3: Tomcat listens on port 8080
Accepts the socket connection.

### Step 4: Tomcat parses HTTP request
Creates:
- HttpServletRequest
- HttpServletResponse

### Step 5: Servlet Mapping (via web.xml)

Example:

```xml
<servlet>
   <servlet-name>hello</servlet-name>
   <servlet-class>com.app.HelloServlet</servlet-class>
</servlet>

<servlet-mapping>
   <servlet-name>hello</servlet-name>
   <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

Mapping:

```
/hello → HelloServlet
```

### Step 6: Tomcat calls:

```
doGet(request, response)
```

### Step 7: Response is written

### Step 8: Tomcat sends HTTP response back

---

# 5️⃣ Thread Model in Servlets

Each request runs in a separate thread.

Example:

```
Thread-1 → /hello
Thread-2 → /users
Thread-3 → /orders
```

Important rule:

> Servlets must be stateless.

Why?

Because multiple threads use the same servlet instance.

If you use instance variables:

```java
public class MyServlet extends HttpServlet {
    private int counter = 0;
}
```

Two threads may update `counter` at the same time → Race condition.

---

# 6️⃣ Servlet Lifecycle

Servlet lifecycle is managed by container:

1. `init()` → called once
2. `service()` → called per request
3. `destroy()` → called before shutdown

Important: Only ONE servlet object exists per mapping.

---

# 7️⃣ Problems With Pure Servlets

If building a real application:

- Login
- Database
- JSON
- Validation
- Transactions
- Logging

You must manually:

- Open DB connections
- Close DB connections
- Manage transactions
- Convert JSON
- Handle exceptions

Example:

```java
Connection conn = DriverManager.getConnection(...);
PreparedStatement ps = conn.prepareStatement(...);
```

This repeats everywhere.

Problems:

- Boilerplate code
- Tight coupling
- Hard testing
- No dependency management
- No transaction abstraction

---

# 8️⃣ Enter Spring Framework

Spring solves:

1. Object creation
2. Dependency management
3. Cross-cutting concerns

---

# 9️⃣ Core Concept: IoC Container

Spring introduces:

```
ApplicationContext
```

Instead of manually creating objects:

```java
UserRepository repo = new UserRepository();
UserService service = new UserService(repo);
```

Spring does:

```java
@Service
public class UserService {
   private final UserRepository repo;

   public UserService(UserRepository repo) {
       this.repo = repo;
   }
}
```

Spring:

- Creates objects (Beans)
- Injects dependencies
- Manages lifecycle

This is Dependency Injection.

---

# 🔟 Spring MVC

Instead of writing Servlets directly:

```java
protected void doGet(...)
```

We write:

```java
@Controller
public class HelloController {

   @GetMapping("/hello")
   public String hello() {
      return "hello";
   }
}
```

---

# 1️⃣1️⃣ DispatcherServlet (Front Controller Pattern)

Spring MVC uses a single servlet:

```
DispatcherServlet
```

All requests go through it.

Flow:

1. Request hits Tomcat
2. Tomcat forwards to DispatcherServlet
3. DispatcherServlet:
    - Uses HandlerMapping
    - Uses HandlerAdapter
    - Calls controller method
    - Returns ModelAndView
4. ViewResolver resolves view
5. Response sent back

---

# 1️⃣2️⃣ Problems Still Remaining in Spring (Pre-Boot)

Even with Spring MVC, we still needed:

- web.xml
- DispatcherServlet configuration
- ViewResolver configuration
- Component scan configuration
- DataSource configuration
- TransactionManager configuration
- Dependency version management

Example XML:

```xml
<context:component-scan base-package="com.app"/>
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
```

Problems:

- Too much configuration
- Too many XML files
- Version conflicts in dependencies
- WAR deployment to external server required

---

# Summary of Part 1

Evolution:

Raw Socket → Servlet → Spring MVC

Each layer reduced manual coding.

But configuration complexity remained high.

---


