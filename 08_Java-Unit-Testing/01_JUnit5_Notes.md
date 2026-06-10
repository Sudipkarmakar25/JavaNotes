# JUnit 5 Notes

## 1. Introduction

### What is JUnit
JUnit is the most widely used testing framework for Java. JUnit 5 is the modern version used for writing and running unit tests.

### Why JUnit is Used
- Automates testing of Java code.
- Helps catch bugs early.
- Supports refactoring with confidence.
- Integrates well with Maven, Gradle, IDEs, and CI/CD tools.
- Improves code quality and maintainability.

### Unit Testing vs Integration Testing

| Type | Meaning | Scope | Example |
|---|---|---|---|
| Unit Test | Tests one small unit of code | method/class level | test `Calculator.add()` |
| Integration Test | Tests multiple components together | DB, API, service flow | test service + repository |

### Interview Note
- Unit tests should be fast and isolated.
- Integration tests are slower but validate real component interaction.

---

## 2. Basic Test Structure

### `@Test`
**Purpose:** Marks a method as a test case.  
**Syntax:**
```
@Test
void methodName() { }
```

### Small Example
```
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {
	@Test
	void shouldAddTwoNumbers() {
		int result = 2 + 3;
		assertEquals(5, result);
	}
}
```

### Practical Note
- Common pattern: **Arrange -> Act -> Assert**.
- Test names should describe behavior, not implementation.

---

## 3. Assertions

Assertions are used to verify expected output.

| Assertion | Purpose | Small Example |
|---|---|---|
| `assertEquals` | checks expected == actual | `assertEquals(5, sum);` |
| `assertTrue` | checks condition is true | `assertTrue(age > 18);` |
| `assertFalse` | checks condition is false | `assertFalse(list.isEmpty());` |
| `assertNull` | checks value is null | `assertNull(user.getMiddleName());` |
| `assertNotNull` | checks value is not null | `assertNotNull(user);` |
| `assertThrows` | checks exception is thrown | `assertThrows(IllegalArgumentException.class, () -> service.save(null));` |

### Example
```
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class AssertionDemoTest {
	@Test
	void shouldValidateAssertions() {
		assertEquals(4, 2 + 2);
		assertTrue(10 > 5);
		assertFalse("java".isEmpty());
		assertNull(null);
		assertNotNull(new Object());
		assertThrows(ArithmeticException.class, () -> {
			int x = 10 / 0;
		});
	}
}
```

### Interview Note
- `assertThrows` is very common in service-layer and validation testing.
- Keep expected value first, actual value second in `assertEquals(expected, actual)`.

---

## 4. Lifecycle Annotations

### `@BeforeEach`
**Purpose:** Runs before every test method.  
**Syntax:** `@BeforeEach`

### `@AfterEach`
**Purpose:** Runs after every test method.  
**Syntax:** `@AfterEach`

### `@BeforeAll`
**Purpose:** Runs once before all tests.  
**Syntax:** `@BeforeAll`

### `@AfterAll`
**Purpose:** Runs once after all tests.  
**Syntax:** `@AfterAll`

### Small Example
```
import org.junit.jupiter.api.*;

class LifecycleTest {
	@BeforeAll
	static void initAll() {
		System.out.println("Before all");
	}

	@BeforeEach
	void init() {
		System.out.println("Before each");
	}

	@Test
	void testOne() { }

	@AfterEach
	void cleanup() {
		System.out.println("After each");
	}

	@AfterAll
	static void cleanupAll() {
		System.out.println("After all");
	}
}
```

### Interview Note
- `@BeforeEach` is for fresh setup per test.
- `@BeforeAll` is for expensive one-time setup.

---

## 5. Common Annotations

### `@DisplayName`
**Purpose:** Gives a readable custom name to a test.  
**Syntax:** `@DisplayName("valid login test")`

**Example:**
```
@Test
@DisplayName("should create user successfully")
void createUserTest() { }
```

### `@Disabled`
**Purpose:** Skips a test temporarily.  
**Syntax:** `@Disabled("reason")`

**Example:**
```
@Test
@Disabled("Pending bug fix")
void disabledTest() { }
```

### `@Nested`
**Purpose:** Groups related tests inside an inner class.  
**Syntax:** `@Nested`

**Example:**
```
class UserServiceTest {
	@Nested
	class CreateUserTests {
		@Test
		void shouldCreateUser() { }
	}
}
```

### `@Tag`
**Purpose:** Categorizes tests.  
**Syntax:** `@Tag("unit")`

**Example:**
```
@Test
@Tag("unit")
void fastTest() { }
```

### `@RepeatedTest`
**Purpose:** Runs the same test multiple times.  
**Syntax:** `@RepeatedTest(3)`

**Example:**
```
@RepeatedTest(3)
void repeatTest() { }
```

### `@ParameterizedTest`
**Purpose:** Runs the same test with different inputs.  
**Syntax:** `@ParameterizedTest`

**Example:**
```
@ParameterizedTest
@ValueSource(strings = {"java", "spring"})
void shouldNotBeBlank(String value) {
	assertFalse(value.isBlank());
}
```

### `@ValueSource`
**Purpose:** Supplies single primitive/string values.  
**Syntax:** `@ValueSource(ints = {1, 2, 3})`

**Example:**
```
@ParameterizedTest
@ValueSource(ints = {2, 4, 6})
void shouldBeEven(int num) {
	assertEquals(0, num % 2);
}
```

### `@CsvSource`
**Purpose:** Supplies multiple arguments in CSV form.  
**Syntax:** `@CsvSource({"2,3,5", "1,4,5"})`

**Example:**
```
@ParameterizedTest
@CsvSource({"2,3,5", "1,4,5"})
void shouldAddNumbers(int a, int b, int expected) {
	assertEquals(expected, a + b);
}
```

### `@MethodSource`
**Purpose:** Supplies arguments from a method.  
**Syntax:** `@MethodSource("methodName")`

**Example:**
```
@ParameterizedTest
@MethodSource("names")
void shouldCheckName(String name) {
	assertNotNull(name);
}

static Stream<String> names() {
	return Stream.of("Amit", "Rahul");
}
```

### Interview Note
- `@ParameterizedTest` is useful when only input changes but logic remains the same.
- `@MethodSource` is the most flexible source.

---

## 6. Parameterized Tests

### Why Use Them
- Avoid duplicate test methods.
- Improve readability.
- Cover multiple inputs with one test.
- Useful for validation, parsing, and edge-case testing.

### Common Sources
- `@ValueSource`
- `@CsvSource`
- `@MethodSource`

### Small Example
```
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.*;

class ParameterizedDemoTest {
	@ParameterizedTest
	@CsvSource({"2,3,5", "10,5,15"})
	void shouldAddValues(int a, int b, int expected) {
		assertEquals(expected, a + b);
	}
}
```

### Interview Note
- Use parameterized tests when test flow is same but inputs vary.

---

## 7. Assertions vs Assumptions

| Aspect | Assertions | Assumptions |
|---|---|---|
| Purpose | Validate result | Check whether test should continue |
| Failure effect | test fails | test skipped/aborted |
| Use case | business logic validation | environment-dependent tests |

### Example
```
import static org.junit.jupiter.api.Assumptions.*;

@Test
void runOnlyOnDev() {
	assumeTrue("dev".equals(System.getProperty("env")));
	assertTrue(true);
}
```

### Interview Note
- Assertions verify correctness.
- Assumptions verify environment or preconditions.

---

## 8. Best Practices

- Keep unit tests small and focused.
- Test one behavior per test method.
- Use meaningful test method names.
- Avoid dependency on database, network, or external systems in unit tests.
- Prefer `assertThrows` for exception testing.
- Use `@BeforeEach` for reusable setup.
- Use parameterized tests to avoid repetition.
- Make tests deterministic; avoid random/flaky behavior.

---

## 9. Top Interview Questions

### 1. Difference between `@BeforeAll` and `@BeforeEach`?
`@BeforeAll` runs once before all tests; `@BeforeEach` runs before every test method.

### 2. Why `@BeforeAll` must be static?
Because JUnit calls it once before creating test class instances in the default lifecycle.

### 3. When should parameterized tests be used?
When the same test logic should run for multiple input values.

### 4. Assertions vs Assumptions?
Assertions fail a test when expectation is wrong; assumptions skip or abort a test when preconditions are not met.

### 5. Why is JUnit important in interviews and production?
It is the standard framework for automated testing in Java projects and is heavily used in backend and Spring Boot applications.

### 6. Difference between unit test and integration test?
Unit test checks isolated code; integration test checks interaction between components.

---

## 10. Quick Revision Table

| Annotation | Purpose | Example |
|---|---|---|
| `@Test` | marks test method | `@Test void testAdd(){}` |
| `@BeforeEach` | runs before every test | `@BeforeEach void setup(){}` |
| `@AfterEach` | runs after every test | `@AfterEach void cleanup(){}` |
| `@BeforeAll` | runs once before all tests | `@BeforeAll static void init(){}` |
| `@AfterAll` | runs once after all tests | `@AfterAll static void end(){}` |
| `@DisplayName` | custom readable name | `@DisplayName("login test")` |
| `@Disabled` | skips test | `@Disabled("bug pending")` |
| `@Nested` | groups related tests | `@Nested class LoginTests {}` |
| `@Tag` | categorizes tests | `@Tag("unit")` |
| `@RepeatedTest` | runs test multiple times | `@RepeatedTest(3)` |
| `@ParameterizedTest` | runs with multiple inputs | `@ParameterizedTest` |
| `@ValueSource` | single-value input source | `@ValueSource(strings = {"a"})` |
| `@CsvSource` | multi-column input source | `@CsvSource({"1,2,3"})` |
| `@MethodSource` | method-based input source | `@MethodSource("data")` |

---

## 11. Final Takeaways

- JUnit 5 is the standard Java testing framework.
- Learn `@Test`, assertions, lifecycle methods, and parameterized tests well.
- In interviews, focus on practical use of annotations, test isolation, and assertion strategy.
- Good tests are readable, repeatable, and fast.

