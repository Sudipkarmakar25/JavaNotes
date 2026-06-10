# Spring HandlerInterceptor – Complete Guide

---

# 1️⃣ What is an Interceptor?

A **HandlerInterceptor** is a component in Spring MVC that intercepts HTTP requests **after the request enters Spring but before it reaches the controller**.

Interceptors allow developers to perform processing such as:

- Authentication
- Authorization
- Logging
- Request timing
- Pre-processing requests
- Post-processing responses

Unlike Filters, **Interceptors are part of Spring MVC**.

---

# 2️⃣ Where Interceptors Sit in the Request Lifecycle

Understanding the request flow is critical.

```
Client Request
      ↓
Servlet Filter
      ↓
DispatcherServlet
      ↓
HandlerInterceptor
      ↓
Controller
      ↓
Service (@Transactional)
      ↓
Repository
      ↓
Database
```

Interceptors run **inside the Spring MVC framework**, after the request enters the `DispatcherServlet`.

---

# 3️⃣ Why Interceptors Are Used

Interceptors help implement **cross-cutting concerns specific to controllers**.

Common use cases:

- Authentication checks
- Logging requests
- Rate limiting
- Performance monitoring
- Modifying model data before response

Example:

Checking if a user is authenticated before accessing a controller.

---

# 4️⃣ HandlerInterceptor Interface

Spring provides the `HandlerInterceptor` interface.

It contains three main methods:

```java
public interface HandlerInterceptor {

    boolean preHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler
    );

    void postHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        ModelAndView modelAndView
    );

    void afterCompletion(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        Exception ex
    );
}
```

---

# 5️⃣ preHandle()

`preHandle()` runs **before the controller method executes**.

Example:

```java
@Override
public boolean preHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler) {

    System.out.println("PreHandle: Request received");

    return true;
}
```

Important:

- Returning `true` → continue processing
- Returning `false` → stop request

This method is often used for **authentication or validation**.

---

# 6️⃣ postHandle()

`postHandle()` runs **after the controller executes but before the response is returned**.

Example:

```java
@Override
public void postHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        ModelAndView modelAndView) {

    System.out.println("PostHandle: Controller executed");
}
```

Use cases:

- Modifying response
- Adding attributes to model

---

# 7️⃣ afterCompletion()

`afterCompletion()` runs **after the entire request has finished processing**.

Example:

```java
@Override
public void afterCompletion(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler,
        Exception ex) {

    System.out.println("AfterCompletion: Request finished");
}
```

Use cases:

- Cleaning resources
- Logging request completion
- Performance metrics

---

# 8️⃣ Interceptor Execution Flow

Interceptor lifecycle:

```
Request
   ↓
preHandle()
   ↓
Controller
   ↓
postHandle()
   ↓
View Rendering
   ↓
afterCompletion()
```

---

# 9️⃣ Creating an Interceptor

Example:

```java
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

@Component
public class LoggingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler) {

        System.out.println("Request URI: " + request.getRequestURI());
        return true;
    }
}
```

---

# 🔟 Registering an Interceptor

Interceptors must be registered using `WebMvcConfigurer`.

Example:

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final LoggingInterceptor loggingInterceptor;

    public WebConfig(LoggingInterceptor loggingInterceptor) {
        this.loggingInterceptor = loggingInterceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loggingInterceptor);
    }
}
```

---

# 1️⃣1️⃣ Limiting Interceptor to Specific Paths

Example:

```java
registry.addInterceptor(loggingInterceptor)
        .addPathPatterns("/api/**")
        .excludePathPatterns("/login");
```

This allows fine-grained control over request interception.

---

# 1️⃣2️⃣ Filter vs Interceptor

| Feature | Filter | Interceptor |
|-------|-------|-------------|
| Layer | Servlet container | Spring MVC |
| Runs before DispatcherServlet | Yes | No |
| Spring context access | Limited | Full |
| Access controller info | No | Yes |
| Typical use | Logging, security | Controller logic |

---

# 1️⃣3️⃣ Real-World Example – Authentication Interceptor

Example:

```java
@Component
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler) throws Exception {

        String token = request.getHeader("Authorization");

        if (token == null) {
            response.setStatus(401);
            return false;
        }

        return true;
    }
}
```

This prevents unauthorized access to controllers.

---

# 1️⃣4️⃣ Common Use Cases

Interceptors are commonly used for:

- Authentication
- Request logging
- Performance monitoring
- Rate limiting
- API gateway behavior

---

# 1️⃣5️⃣ Key Concept to Remember

Filters operate at the **Servlet level**.

Interceptors operate at the **Spring MVC level**.

This means interceptors can access:

- Controller information
- Handler methods
- Model objects

---

# 1️⃣6️⃣ Interview Questions

- What is HandlerInterceptor?
- Difference between Filter and Interceptor?
- Explain preHandle, postHandle, afterCompletion.
- When would you use an interceptor?
- How to register interceptor in Spring Boot?
- What happens if preHandle returns false?

---

# 1️⃣7️⃣ Interview Summary (≈30 words)

Spring HandlerInterceptors intercept HTTP requests inside the Spring MVC lifecycle, enabling pre-processing, post-processing, and completion handling around controller execution for concerns like authentication, logging, and request monitoring.

---