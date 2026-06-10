# Servlet Filters – Complete Guide

---

# 1️⃣ What is a Filter?

A **Filter** is a component that intercepts HTTP requests and responses **before they reach the application**.

Filters are part of the **Servlet specification**, not Spring itself.

They allow developers to perform processing such as:

- Logging
- Authentication
- Request modification
- Response modification
- Compression
- CORS handling

Filters work **before the request reaches the DispatcherServlet** in Spring.

---

# 2️⃣ Where Filters Sit in the Request Lifecycle

Understanding this flow is very important.

```
Client Request
      ↓
Servlet Filter
      ↓
DispatcherServlet (Spring)
      ↓
HandlerInterceptor
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Database
```

Filters are the **first processing layer inside the application server**.

---

# 3️⃣ Why Filters Are Used

Filters are useful for **cross-cutting concerns** that apply to every request.

Common use cases:

- Request logging
- Authentication
- Input validation
- Request timing
- CORS handling
- Response compression

Example:

Logging every request.

---

# 4️⃣ Filter Interface

Filters implement the `Filter` interface.

```java
public interface Filter {

    void init(FilterConfig filterConfig);

    void doFilter(
        ServletRequest request,
        ServletResponse response,
        FilterChain chain
    );

    void destroy();
}
```

The most important method is:

```
doFilter()
```

---

# 5️⃣ The `doFilter()` Method

This method intercepts the request.

Example:

```java
import jakarta.servlet.*;
import java.io.IOException;

public class LoggingFilter implements Filter {

    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain) throws IOException, ServletException {

        System.out.println("Request received");

        chain.doFilter(request, response);

        System.out.println("Response sent");
    }
}
```

Important line:

```
chain.doFilter(request, response);
```

This passes the request to the **next filter or controller**.

---

# 6️⃣ Filter Chain

Multiple filters can exist in an application.

They form a **chain**.

Example flow:

```
Request
   ↓
Filter A
   ↓
Filter B
   ↓
Filter C
   ↓
DispatcherServlet
```

Each filter must call:

```
chain.doFilter()
```

Otherwise the request stops.

---

# 7️⃣ Registering Filters in Spring Boot

Filters can be registered using `@Component`.

Example:

```java
import jakarta.servlet.*;
import org.springframework.stereotype.Component;
import java.io.IOException;

@Component
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(
        ServletRequest request,
        ServletResponse response,
        FilterChain chain
    ) throws IOException, ServletException {

        System.out.println("Filter executed");

        chain.doFilter(request, response);
    }
}
```

Spring Boot automatically registers it.

---

# 8️⃣ OncePerRequestFilter (Recommended)

Spring provides a helper class:

```
OncePerRequestFilter
```

It ensures the filter runs **only once per request**.

Example:

```java
import org.springframework.web.filter.OncePerRequestFilter;

public class LoggingFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain filterChain
    ) throws ServletException, IOException {

        System.out.println("Filter executed");

        filterChain.doFilter(request, response);
    }
}
```

This is commonly used in **Spring Security filters**.

---

# 9️⃣ Filter vs Interceptor (Preview)

| Feature | Filter | Interceptor |
|-------|-------|-------------|
| Layer | Servlet container | Spring MVC |
| Runs before DispatcherServlet | Yes | No |
| Access to Spring beans | Limited | Full |
| Typical use | Logging, security | Controller logic |

Filters operate **outside Spring MVC**.

Interceptors operate **inside Spring MVC**.

---

# 🔟 Real-World Example – Logging Filter

Example:

```java
@Component
public class RequestLoggingFilter implements Filter {

    public void doFilter(
        ServletRequest request,
        ServletResponse response,
        FilterChain chain
    ) throws IOException, ServletException {

        long start = System.currentTimeMillis();

        chain.doFilter(request, response);

        long end = System.currentTimeMillis();

        System.out.println("Request processed in: " + (end - start) + " ms");
    }
}
```

This measures **request execution time**.

---

# 1️⃣1️⃣ Common Built-in Filters

Common filters used in Spring Boot:

- CORS filter
- Spring Security filter chain
- HiddenHttpMethodFilter
- CharacterEncodingFilter

These handle common web concerns.

---

# 1️⃣2️⃣ When to Use Filters

Use filters when logic must run:

- Before Spring MVC
- For all requests
- At servlet container level

Example:

- security gateway
- request logging
- compression

---

# 1️⃣3️⃣ Limitations of Filters

Filters:

- Cannot access controller method directly
- Limited Spring context access
- Not aware of handler mapping

For controller-specific logic, use **interceptors**.

---

# 1️⃣4️⃣ Interview Questions

- What is a Servlet Filter?
- Where does filter execute in request lifecycle?
- What is FilterChain?
- Difference between Filter and Interceptor?
- Why use OncePerRequestFilter?
- What happens if `chain.doFilter()` is not called?

---

# 1️⃣5️⃣ Key Concept to Remember

Filters operate at the **Servlet container level**.

They intercept requests **before Spring processes them**.

---

# 1️⃣6️⃣ Interview Summary (≈30 words)

Servlet Filters intercept HTTP requests and responses at the servlet container level before they reach Spring MVC, enabling cross-cutting concerns such as logging, authentication, and request modification.

---