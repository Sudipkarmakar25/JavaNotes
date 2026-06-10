# Spring Boot Interview Questions (Excluding JPA, Hibernate, Security)

## Core Spring Boot Concepts
- What is Spring Boot and how is it different from Spring Framework?
- What problems does Spring Boot solve?
- What is auto-configuration in Spring Boot?
- How does `@SpringBootApplication` work internally?
- What are the main components of Spring Boot?
- What is the difference between `@Component`, `@Service`, and `@RestController`?
- What is dependency injection and how is it implemented in Spring Boot?
- What is inversion of control (IoC)?

## Configuration & Properties
- What is `application.properties` vs `application.yml`?
- How does Spring Boot load configuration properties?
- What is `@Value` and `@ConfigurationProperties`?
- What are profiles in Spring Boot?
- How do you activate different profiles?
- What is the priority order of configuration sources?

## Auto Configuration & Internals
- How does Spring Boot auto-configuration work internally?
- What is `spring.factories` / `AutoConfiguration.imports`?
- What is `@EnableAutoConfiguration`?
- How do you disable auto-configuration for specific classes?
- What are conditional annotations (`@ConditionalOnProperty`, etc.)?

## Starter Dependencies
- What are Spring Boot starters?
- How do starters simplify dependency management?
- Can you create your own custom starter?

## REST APIs & Web
- How do you create a REST API in Spring Boot?
- Difference between `@Controller` and `@RestController`?
- What is `@RequestMapping` vs `@GetMapping`?
- How does request handling work internally?
- What is `@PathVariable` vs `@RequestParam`?
- What is `@RequestBody` and how does it work?
- What is content negotiation?
- How does Spring Boot handle JSON conversion?

## Exception Handling
- How do you handle exceptions globally?
- What is `@ControllerAdvice`?
- What is `@ExceptionHandler`?
- How do you customize error responses?
- What is default error handling in Spring Boot?

## Validation
- How do you validate request data?
- What is `@Valid` / `@Validated`?
- How do you handle validation errors?
- Difference between `@NotNull`, `@NotEmpty`, `@NotBlank`?

## Bean Lifecycle & Context
- What is ApplicationContext?
- What is Bean lifecycle in Spring Boot?
- What are Bean scopes?
- What is `@PostConstruct` and `@PreDestroy`?
- How does Spring manage beans internally?

## Logging & Monitoring
- What logging framework does Spring Boot use by default?
- How do you configure logging levels?
- What is Spring Boot Actuator?
- What endpoints does Actuator provide?
- How do you expose custom metrics?

## Embedded Server
- What embedded servers does Spring Boot support?
- How do you change the default port?
- How do you configure Tomcat/Jetty?
- How does Spring Boot start without external server?

## DevTools & Productivity
- What is Spring Boot DevTools?
- How does hot reloading work?

## Testing
- How do you test Spring Boot applications?
- What is `@SpringBootTest`?
- Difference between `@WebMvcTest` and `@SpringBootTest`?
- What is MockMvc?
- How do you mock dependencies?

## Microservices & Communication
- How do Spring Boot services communicate?
- What is RestTemplate vs WebClient?
- What is WebFlux?
- Difference between synchronous and reactive programming?

## Caching
- What is caching in Spring Boot?
- How do you enable caching?
- What is `@Cacheable`, `@CacheEvict`?

## Scheduling & Async
- How do you schedule tasks in Spring Boot?
- What is `@Scheduled`?
- What is `@Async`?
- How do you enable async processing?

## Deployment & Packaging
- How do you package a Spring Boot application?
- What is a fat JAR?
- How do you deploy Spring Boot applications?
- Difference between WAR and JAR?

## Advanced / Internals (High Value)
- How does DispatcherServlet work internally?
- What is HandlerMapping and HandlerAdapter?
- What happens when a request hits Spring Boot?
- How does Spring Boot scan components?
- What is classpath scanning?
- How does Spring Boot manage dependencies via BOM?
- What is lazy initialization?
- What is circular dependency and how does Spring handle it?

## Real-World / Scenario-Based
- How do you design a scalable REST API?
- How do you handle high traffic in Spring Boot?
- How do you implement rate limiting?
- How do you structure a production-ready Spring Boot project?
- How do you externalize configuration for different environments?
- How do you debug a slow Spring Boot API?