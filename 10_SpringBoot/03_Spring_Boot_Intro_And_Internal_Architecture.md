# Spring & Spring Boot – Complete Lecture (Part 2)
## Spring Boot Deep Dive – Internals & Architecture

---

# 1️⃣ Why Spring Boot Was Needed

In traditional Spring (Part 1), we had:

- web.xml
- DispatcherServlet configuration
- ViewResolver configuration
- Component scan configuration
- DataSource configuration
- TransactionManager configuration
- Manual dependency version management
- WAR packaging and external Tomcat deployment

Spring was powerful.

But it was too configurable.

If Spring is a box of Lego pieces,
Spring Boot is a pre-assembled Lego model — still customizable.

---

# 2️⃣ What Is Spring Boot?

Spring Boot is NOT a new framework.

It is:

> An opinionated layer on top of Spring Framework that eliminates boilerplate configuration.

```text
What is "opinionated springboot"?
"Spring is opinionated" is a common way to describe Spring Boot's use of "opinionated defaults" and "auto-configuration",
 where it makes numerous design choices and configuration decisions for the developer based on best practices and conventions. 

This approach means that the framework has a "mind of its own" about how things should work, providing a well-paved path
 that works for most common use cases. 
```
It solves three major problems:

1. Auto-Configuration
2. Starter Dependencies
3. Embedded Server

---

# 3️⃣ Minimal Spring Boot Application

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

That’s it.

No web.xml.
No DispatcherServlet configuration.
No server setup.

---

# 4️⃣ What Does @SpringBootApplication Actually Do?

It is a combination of:

```java
@Configuration
@ComponentScan
@EnableAutoConfiguration
```

Let’s break this down.

---

## 4.1 @Configuration

Marks the class as a source of bean definitions.

Equivalent to XML:

```xml
<beans>
</beans>
```

---

## 4.2 @ComponentScan

Automatically scans the package and subpackages for:

- @Component
- @Service
- @Repository
- @Controller
- @RestController

This eliminates manual bean registration.

---

## 4.3 @EnableAutoConfiguration (THE MAGIC)

This is the heart of Spring Boot.

It tells Spring:

> “Based on the dependencies in classpath, configure beans automatically.”

---

# 5️⃣ How Auto-Configuration Works Internally

This is critical to understand.

When Spring Boot starts:

### Step 1: It reads `spring.factories`

Located inside:
```
META-INF/spring.factories
```

Inside this file:

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
...
```

This is basically a list of configuration classes.

---

### Step 2: Conditional Bean Loading

Auto-configuration classes use:

```java
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnProperty
```

Example:

```java
@ConditionalOnClass(DispatcherServlet.class)
public class WebMvcAutoConfiguration {
}
```

Meaning:

IF DispatcherServlet exists in classpath  
THEN configure Spring MVC automatically.

This is conditional bean registration.

---

# 6️⃣ Classpath-Based Configuration

Spring Boot scans your dependencies.

If you include:

```
spring-boot-starter-web
```

Classpath now contains:

- Spring MVC
- Jackson
- Validation API
- Embedded Tomcat

Spring Boot sees these classes,
and activates corresponding auto-configurations.

No XML required.

---

# 7️⃣ Starter Dependencies

Before Spring Boot, you manually added:

- spring-core
- spring-context
- spring-web
- spring-webmvc
- jackson
- validation
- logging

And you had to match versions.

Now you add:

```
spring-boot-starter-web
```

This starter:

- Pulls compatible versions
- Avoids version conflicts
- Simplifies dependency management

It uses a BOM (Bill of Materials) to ensure compatibility.

---

# 8️⃣ Embedded Server – Game Changer

Traditional Spring:

```
WAR → External Tomcat
```

Spring Boot:

```
JAR → Embedded Tomcat
```

When you run:

```java
SpringApplication.run(Application.class, args);
```

Spring Boot:

1. Creates ApplicationContext
2. Starts embedded Tomcat
3. Registers DispatcherServlet automatically
4. Binds to port 8080

Your app is now self-contained.

This enabled:

- Microservices
- Docker deployment
- Cloud-native
