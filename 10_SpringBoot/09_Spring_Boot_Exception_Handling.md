# Spring Boot Exception Handling – Complete Guide with Mini Project

---

# 1. What is Exception Handling in Spring?

**Exception handling** in Spring Boot provides a **centralized, clean, and consistent** way to manage runtime errors in REST APIs instead of returning raw stack traces.

It helps in:

- Returning **proper HTTP status codes**
- Sending **structured error responses**
- Keeping **controllers clean**
- Improving **API reliability and debugging**

---

# 2. Default Spring Boot Behavior (Problem)

Without custom handling:

- Spring returns **whitelabel error page**
- Stack traces may be exposed
- Error format is **not API-friendly**

Hence, we implement **Global Exception Handling**.

---

# 3. Key Annotations Used

## `@ExceptionHandler`
Handles **specific exception inside a controller**.

## `@ControllerAdvice`
Creates a **global exception handler** across the application.

## `@RestControllerAdvice`
Same as above but returns **JSON response** directly (most used in REST APIs).

## `@ResponseStatus`
Maps an exception to a **fixed HTTP status**.

---

# 4. Standard Error Response Structure

A clean API error response usually contains:

```json
{
  "timestamp": "2026-01-01T10:00:00",
  "status": 404,
  "error": "NOT_FOUND",
  "message": "User not found",
  "path": "/users/10"
}
```

---

# 5. Mini Project – User Management API with Global Exception Handling

## 5.1 Project Structure

```
com.example.demo
 ├── controller
 ├── service
 ├── repository
 ├── entity
 ├── exception
 └── DemoApplication
```

---

# 6. Step-by-Step Implementation

## 6.1 Entity

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
    private String email;

    // getters & setters
}
```

---

## 6.2 Repository

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

---

## 6.3 Custom Exception

```java
public class UserNotFoundException extends RuntimeException {

    public UserNotFoundException(Long id) {
        super("User not found with id: " + id);
    }
}
```

---

## 6.4 Service Layer

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User getUser(Long id) {
        return repository.findById(id)
                .orElseThrow(() -> new UserNotFoundException(id));
    }

    public User createUser(User user) {
        return repository.save(user);
    }
}
```

---

## 6.5 Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return service.getUser(id);
    }

    @PostMapping
    public User create(@RequestBody User user) {
        return service.createUser(user);
    }
}
```

---

# 7. Global Exception Handler

## 7.1 Error Response DTO

```java
public class ErrorResponse {

    private LocalDateTime timestamp;
    private int status;
    private String error;
    private String message;
    private String path;

    public ErrorResponse(int status, String error, String message, String path) {
        this.timestamp = LocalDateTime.now();
        this.status = status;
        this.error = error;
        this.message = message;
        this.path = path;
    }

    // getters
}
```

---

## 7.2 Global Handler using `@RestControllerAdvice`

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(
            UserNotFoundException ex,
            HttpServletRequest request) {

        ErrorResponse error = new ErrorResponse(
                404,
                "NOT_FOUND",
                ex.getMessage(),
                request.getRequestURI()
        );

        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(
            Exception ex,
            HttpServletRequest request) {

        ErrorResponse error = new ErrorResponse(
                500,
                "INTERNAL_SERVER_ERROR",
                ex.getMessage(),
                request.getRequestURI()
        );

        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

# 8. Validation Exception Handling (Best Practice)

## Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## Add Validation in Entity

```java
@NotBlank
private String name;

@Email
private String email;
```

## Handle Validation Errors

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<Map<String, String>> handleValidation(
        MethodArgumentNotValidException ex) {

    Map<String, String> errors = new HashMap<>();

    ex.getBindingResult().getFieldErrors()
            .forEach(err -> errors.put(err.getField(), err.getDefaultMessage()));

    return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
}
```

---

# 9. Flow of Exception Handling in Spring Boot

```
Controller → Service → Exception Thrown
        ↓
GlobalExceptionHandler (@RestControllerAdvice)
        ↓
Structured JSON Response + Proper HTTP Status
```

---

# 10. Best Practices (Industry Level)

- Always use **Global Exception Handler**
- Never expose **stack traces** in API responses
- Return **structured error JSON**
- Separate **business vs system exceptions**
- Log errors using **SLF4J**
- Use **validation annotations** for request data

---

# 11. Interview-Ready Summary (≈30 words)

Spring Boot exception handling uses `@RestControllerAdvice` and `@ExceptionHandler` to provide centralized, structured, and secure error responses with proper HTTP status codes, improving API reliability, debugging, and maintainability in production systems.
