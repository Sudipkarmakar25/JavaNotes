# Spring Annotations Guide – @Primary vs @Qualifier vs @Order

## 1. Problem These Annotations Solve

When **multiple beans of the same type** exist in Spring, dependency injection becomes **ambiguous**.

Spring must decide:

- **Which bean to inject?** → `@Primary` / `@Qualifier`
- **In what execution order?** → `@Order`

---

## 2. `@Primary`

### Definition
`@Primary` marks a bean as the **default choice** when multiple beans of the same type are available.

### Example

```java
public interface PaymentService {
    String pay();
}
```

```java
@Service
@Primary
public class CardPaymentService implements PaymentService {
    public String pay() {
        return "Paid using Card";
    }
}
```

```java
@Service
public class UpiPaymentService implements PaymentService {
    public String pay() {
        return "Paid using UPI";
    }
}
```

```java
@RestController
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(PaymentService paymentService) {
        this.paymentService = paymentService; // CardPaymentService injected
    }
}
```

### Key Points

- Used when **one bean should be default**
- Avoids ambiguity **without changing injection code**
- Can be overridden by `@Qualifier`

---

## 3. `@Qualifier`

### Definition
`@Qualifier` explicitly tells Spring **which exact bean to inject**.

### Example

```java
@Service("cardService")
public class CardPaymentService implements PaymentService {
    public String pay() {
        return "Paid using Card";
    }
}
```

```java
@Service("upiService")
public class UpiPaymentService implements PaymentService {
    public String pay() {
        return "Paid using UPI";
    }
}
```

```java
@RestController
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(@Qualifier("upiService") PaymentService paymentService) {
        this.paymentService = paymentService; // UpiPaymentService injected
    }
}
```

### Key Points

- Provides **precise control**
- Overrides `@Primary`
- Required when **multiple beans must be chosen dynamically**

---

## 4. `@Primary vs @Qualifier`

| Feature | `@Primary` | `@Qualifier` |
|---------|------------|--------------|
| Purpose | Default bean selection | Explicit bean selection |
| Ambiguity handling | Automatic | Manual |
| Overrides | Can be overridden | Always wins |
| Use case | One common default | Multiple specific choices |

**Rule:**  
If both are present → **`@Qualifier` takes priority**.

---

## 5. `@Order`

### Definition
`@Order` defines the **execution or processing order** of multiple beans, commonly used in:

- Filters
- Interceptors
- Event listeners
- Collections of beans (`List<BeanType>`)

### Example

```java
@Component
@Order(1)
public class FirstProcessor implements Processor {
    public void process() {
        System.out.println("First");
    }
}
```

```java
@Component
@Order(2)
public class SecondProcessor implements Processor {
    public void process() {
        System.out.println("Second");
    }
}
```

```java
@Component
public class Runner {

    public Runner(List<Processor> processors) {
        processors.forEach(Processor::process);
    }
}
```

### Output

```
First
Second
```

### Key Points

- **Lower value = higher priority**
- Works only when **multiple beans are injected as a collection**
- Does **not choose bean**, only **orders execution**

---

## 6. When to Use What?

- Use **`@Primary`** → when one bean is the **default choice**
- Use **`@Qualifier`** → when you must **explicitly select a bean**
- Use **`@Order`** → when **multiple beans must run in sequence**

---

## 7. Interview-Ready Summary (30 words)

`@Primary` sets the default bean, `@Qualifier` selects a specific bean explicitly, and `@Order` controls execution sequence among multiple beans. Together they resolve ambiguity and manage dependency injection behavior in Spring.
