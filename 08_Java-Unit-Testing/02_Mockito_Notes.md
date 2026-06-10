# Mockito Notes

## 1. Introduction

### What is Mockito
Mockito is the most widely used mocking framework in Java. It is used to create mock objects for unit testing.

### Why Mocking is Needed
- Real dependencies like database, API clients, mail services, and message brokers make tests slow.
- External systems can fail for reasons unrelated to business logic.
- Mocking helps isolate the class under test.
- It makes unit tests fast, repeatable, and focused.

### Mock vs Stub vs Spy

| Term | Meaning | Real-world use |
|---|---|---|
| Mock | Fake object whose behavior and interactions are verified | verify repository call |
| Stub | Object that returns fixed data | return fake user from DAO |
| Spy | Real object wrapped for partial mocking | call real list methods but stub one method |

### Interview Note
- Mockito mainly creates **mocks** and **spies**.
- Stubbing means defining what a mock should return.

---

## 2. Base Example Used in Notes

```
class User {
	private final String email;

	User(String email) {
		this.email = email;
	}

	String getEmail() {
		return email;
	}
}

interface UserRepository {
	User findById(Long id);
	void save(User user);
}

interface EmailService {
	void sendWelcomeEmail(String email);
}

class UserService {
	private final UserRepository userRepository;
	private final EmailService emailService;

	UserService(UserRepository userRepository, EmailService emailService) {
		this.userRepository = userRepository;
		this.emailService = emailService;
	}

	User getUser(Long id) {
		return userRepository.findById(id);
	}

	void register(User user) {
		userRepository.save(user);
		emailService.sendWelcomeEmail(user.getEmail());
	}
}
```

---

## 3. Core Annotations

### `@Mock`
**Purpose:** Creates a mock object.  
**Example:**
```
@Mock
private UserRepository userRepository;
```

### `@InjectMocks`
**Purpose:** Creates the class under test and injects mocks into it.

#### Constructor Injection
Mockito first prefers constructor injection.

**Example:**
```
@Mock
private UserRepository userRepository;

@Mock
private EmailService emailService;

@InjectMocks
private UserService userService;
```

#### Setter Injection
If constructor injection is not possible, Mockito tries setters.

**Example:**
```
class UserService {
	void setEmailService(EmailService emailService) { }
}
```

#### Field Injection
If no constructor or setter is usable, Mockito may inject directly into fields.

**Interview Note**
- Injection order is generally: constructor -> setter -> field.
- In production code, constructor injection is preferred.

### `@Spy`
**Purpose:** Creates a spy around a real object.

**Example:**
```
@Spy
private List<String> names = new ArrayList<>();
```

**Difference from Mock**
- Mock: all methods are fake unless stubbed.
- Spy: real methods run unless stubbed.

### `@Captor`
**Purpose:** Captures arguments passed to a mocked method.

**Example:**
```
@Captor
private ArgumentCaptor<User> userCaptor;
```

### `@ExtendWith(MockitoExtension.class)`
**Purpose:** Enables Mockito annotations in JUnit 5.

**Why Required in JUnit 5**
- Initializes `@Mock`, `@Spy`, `@InjectMocks`, and `@Captor`.
- Replaces older `MockitoAnnotations.initMocks(...)` style.

**Example:**
```
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
	@Mock
	private UserRepository userRepository;
}
```

---

## 4. Stubbing

Stubbing means defining what a mock should do.

### `when().thenReturn()`
**Purpose:** Returns a fixed value.

**Example:**
```
when(userRepository.findById(1L)).thenReturn(new User("a@mail.com"));
```

### `thenThrow()`
**Purpose:** Throws an exception from a mocked method.

**Example:**
```
when(userRepository.findById(99L))
		.thenThrow(new IllegalArgumentException("User not found"));
```

### `thenAnswer()`
**Purpose:** Returns a dynamic value based on invocation.

**Example:**
```
when(userRepository.findById(anyLong()))
		.thenAnswer(invocation -> new User("id-" + invocation.getArgument(0) + "@mail.com"));
```

### `doReturn()`
**Purpose:** Alternative stubbing style, commonly used with spies.

**Example:**
```
doReturn("mocked").when(spyList).get(0);
```

### `doThrow()`
**Purpose:** Throws exception for void methods or spy methods.

**Example:**
```
doThrow(new RuntimeException("Mail failed"))
		.when(emailService).sendWelcomeEmail(anyString());
```

### Interview Note
- Use `when(...).thenReturn(...)` for normal mocks.
- Use `doReturn()` / `doThrow()` mostly for spies and void methods.

---

## 5. Verification

Verification checks whether interactions happened.

### `verify()`
**Purpose:** Verifies a method call happened.

**Example:**
```
verify(userRepository).save(user);
```

### `times()`
**Purpose:** Verifies exact call count.

**Example:**
```
verify(userRepository, times(1)).findById(1L);
```

### `never()`
**Purpose:** Verifies a method was not called.

**Example:**
```
verify(emailService, never()).sendWelcomeEmail(anyString());
```

### `atLeastOnce()`
**Purpose:** Verifies method was called one or more times.

**Example:**
```
verify(userRepository, atLeastOnce()).save(any(User.class));
```

### `atMost()`
**Purpose:** Verifies maximum call count.

**Example:**
```
verify(userRepository, atMost(2)).findById(anyLong());
```

### Interview Note
- `when()` defines behavior.
- `verify()` checks interaction after execution.

---

## 6. Argument Matchers

Argument matchers help stub or verify flexible inputs.

| Matcher | Purpose | Example |
|---|---|---|
| `any()` | matches any object | `any(User.class)` |
| `anyString()` | matches any string | `anyString()` |
| `anyInt()` | matches any int | `anyInt()` |
| `eq()` | matches exact value | `eq(1L)` |

### Example
```
when(userRepository.findById(anyLong())).thenReturn(new User("test@mail.com"));
verify(emailService).sendWelcomeEmail(anyString());
verify(userRepository).findById(eq(1L));
```

### Interview Note
- If you use one matcher in a method call, usually all arguments should use matchers consistently.

---

## 7. ArgumentCaptor

### Purpose
Used when you want to inspect the actual object passed to a mock.

### Practical Example
```
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
	@Mock
	private UserRepository userRepository;

	@Mock
	private EmailService emailService;

	@InjectMocks
	private UserService userService;

	@Captor
	private ArgumentCaptor<User> userCaptor;

	@Test
	void shouldCaptureSavedUser() {
		User user = new User("amit@mail.com");

		userService.register(user);

		verify(userRepository).save(userCaptor.capture());
		assertEquals("amit@mail.com", userCaptor.getValue().getEmail());
	}
}
```

### Interview Note
- `ArgumentCaptor` is useful when equality check is not enough and you need to inspect a specific field.

---

## 8. Mock vs Spy Comparison Table

| Feature | Mock | Spy |
|---|---|---|
| Real object used | No | Yes |
| Default behavior | returns default values | calls real methods |
| Main use | isolate dependency completely | partial mocking |
| Risk | fewer surprises | real method side effects |
| Best for | repository, API client, service dependency | legacy code or partial behavior override |

---

## 9. Common Mistakes

- Using Mockito in integration tests where real dependencies are expected.
- Over-mocking every small object.
- Using spies without understanding that real methods execute.
- Mixing raw values and matchers incorrectly.
- Verifying too many internal calls instead of business behavior.
- Using field injection in production code and depending on Mockito to fix design.
- Using `when(spy.method())` when `doReturn(...).when(spy)...` is safer.

---

## 10. Top Interview Questions

### 1. Mock vs Spy?
Mock is fully fake; spy wraps a real object and calls real methods unless stubbed.

### 2. `@InjectMocks` internal working?
Mockito creates the target object and tries dependency injection using constructor, then setter, then field injection.

### 3. Why use `MockitoExtension`?
It initializes Mockito annotations and integrates Mockito lifecycle with JUnit 5.

### 4. `verify` vs `when`?
`when` defines mock behavior; `verify` checks whether interaction happened.

### 5. `doReturn` vs `thenReturn`?
`thenReturn` is standard stubbing; `doReturn` is safer for spies because it avoids calling the real method during stubbing.

### 6. Why is mocking needed?
To isolate business logic from slow, external, or unstable dependencies.

### 7. When should `ArgumentCaptor` be used?
When you need to inspect the exact argument passed to a mock.

---

## 11. Quick Revision Table

| Annotation/Method | Purpose | Example |
|---|---|---|
| `@Mock` | creates mock object | `@Mock UserRepository repo;` |
| `@InjectMocks` | injects mocks into target | `@InjectMocks UserService service;` |
| `@Spy` | partial mocking with real object | `@Spy List<String> list = new ArrayList<>();` |
| `@Captor` | captures method arguments | `@Captor ArgumentCaptor<User> captor;` |
| `@ExtendWith(MockitoExtension.class)` | enables Mockito in JUnit 5 | `@ExtendWith(MockitoExtension.class)` |
| `when().thenReturn()` | stub return value | `when(repo.findById(1L)).thenReturn(user);` |
| `thenThrow()` | stub exception | `when(repo.findById(1L)).thenThrow(ex);` |
| `thenAnswer()` | dynamic stubbing | `thenAnswer(inv -> ...)` |
| `doReturn()` | stub spy safely | `doReturn(x).when(spy).get(0);` |
| `doThrow()` | stub void/spy exception | `doThrow(ex).when(mock).run();` |
| `verify()` | verify call happened | `verify(repo).save(user);` |
| `times()` | exact invocation count | `verify(repo, times(1)).save(user);` |
| `never()` | verify no invocation | `verify(mail, never()).sendWelcomeEmail(anyString());` |
| `atLeastOnce()` | called one or more times | `verify(repo, atLeastOnce()).save(any());` |
| `atMost()` | max invocation count | `verify(repo, atMost(2)).findById(anyLong());` |
| `any()` | matches any object | `any(User.class)` |
| `anyString()` | matches any string | `anyString()` |
| `anyInt()` | matches any int | `anyInt()` |
| `eq()` | matches exact value | `eq(1L)` |

---

## 12. Final Takeaways

- Mockito helps isolate dependencies and keeps unit tests fast.
- Learn `@Mock`, `@InjectMocks`, `@Spy`, stubbing, and verification well.
- Constructor injection works best with Mockito and also improves production design.
- In interviews, focus on mock vs spy, stubbing vs verification, and argument capture.

