# Spring Profiles – Complete Guide

## 1. What is a Spring Profile?

A **Spring Profile** allows you to define **environment-specific configurations** for applications such as:

- Development (`dev`)
- Testing (`test`)
- Production (`prod`)

This enables running the **same codebase** in multiple environments **without changing code**.

---

## 2. Why Use Spring Profiles?

- Separate **environment configurations**
- Avoid **manual code changes** during deployment
- Improve **security** by isolating production settings
- Support **clean microservice configuration management**

---

## 3. Ways to Configure Spring Profiles

### 3.1 Using `@Profile` Annotation

```java
@Configuration
@Profile("dev")
public class DevConfig {

    @Bean
    public String environmentMessage() {
        return "Running in Development Environment";
    }
}
```

```java
@Configuration
@Profile("prod")
public class ProdConfig {

    @Bean
    public String environmentMessage() {
        return "Running in Production Environment";
    }
}
```

Spring loads beans **only when the matching profile is active**.

---

### 3.2 Profile-Specific Properties Files

Spring Boot automatically loads:

```
application-{profile}.properties
application-{profile}.yml
```

#### Example

**application-dev.properties**
```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/devdb
spring.datasource.username=root
spring.datasource.password=root
```

**application-prod.properties**
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://prod-host:3306/proddb
spring.datasource.username=prod_user
spring.datasource.password=secure_password
```

---

## 4. Activating Spring Profiles

### 4.1 From `application.properties`

```properties
spring.profiles.active=dev
```

### 4.2 From Command Line

```bash
java -jar app.jar --spring.profiles.active=prod
```

### 4.3 Using Environment Variables

**Linux / Mac**
```bash
export SPRING_PROFILES_ACTIVE=dev
```

**Windows**
```cmd
set SPRING_PROFILES_ACTIVE=dev
```

### 4.4 Using JVM Argument

```bash
java -Dspring.profiles.active=test -jar app.jar
```

---

## 5. Multiple Active Profiles

```properties
spring.profiles.active=dev,featureX
```

**Priority order:**

```
application.properties
→ application-dev.properties
→ application-featureX.properties
```

Later profiles **override earlier values**.

---

## 6. Default Profile

If no profile is set, Spring uses:

```
default
```

You can configure:

```properties
spring.profiles.default=dev
```

---

## 7. Profile-Specific Bean Example

```java
public interface PaymentService {
    String pay();
}
```

```java
@Service
@Profile("dev")
public class DevPaymentService implements PaymentService {
    public String pay() {
        return "Dev Payment Gateway";
    }
}
```

```java
@Service
@Profile("prod")
public class ProdPaymentService implements PaymentService {
    public String pay() {
        return "Real Payment Gateway";
    }
}
```

Only **one implementation loads per active profile**.

---

## 8. YAML Configuration with Profiles

```yaml
spring:
  profiles:
    active: dev

---

spring:
  config:
    activate:
      on-profile: dev
server:
  port: 8081

---

spring:
  config:
    activate:
      on-profile: prod
server:
  port: 8080
```

---

## 9. Best Practices

- Never store **production secrets** in code
- Use **environment variables, Vault, or AWS Secrets Manager**
- Maintain **separate databases per environment**
- Use **Spring Cloud Config** in microservices
- Combine with **Docker/Kubernetes environment configs**

---

## 10. Common Interview Questions

### Q1. Difference between `@Profile` and properties-based profiles?
`@Profile` controls **bean loading**, while properties files control **configuration values**.

### Q2. Can multiple profiles be active?
Yes. Spring merges them using **override priority order**.

### Q3. What happens if no profile is active?
Spring uses the **default profile**.

### Q4. Why are profiles important in microservices?
They provide **environment isolation**, **secure configs**, and **flexible deployments**.

---

## 11. Summary

Spring Profiles enable:

- **Environment-specific configuration**
- **Secure production separation**
- **Flexible deployment**
- **Clean microservice configuration management**

They are essential for **real-world Spring Boot applications**.
